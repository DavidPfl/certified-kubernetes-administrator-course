# Backup and Restore Methods

- Take me to [Video Tutorial](https://kodekloud.com/topic/backup-and-restore-methods/)

In this section, we will take a look at backup and restore methods

## Backup Candidates

![bc](../../images/bc.PNG)

## Resource Configuration

- Imperative way

  ![rci](../../images/rci.PNG)

- Declarative Way (Preferred approach)

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: myapp-pod
    labels:
      app: myapp
      type: front-end
  spec:
    containers:
    - name: nginx-container
      image: nginx
  ```

  ![rcd](../../images/rcd.PNG)

- A good practice is to store resource configurations on source code repositories like github.

  ![rcd1](../../images/rcd1.PNG)

## Backup - Resource Configs

- Backing up the resource configs misses resources created imperatively
- To also backup imperatively created resources, query the kube-apiserver as follows and back everything up:

  ```
  kubectl get all --all-namespaces -o yaml > all-deploy-services.yaml (only for few resource groups)
  ```

- There are many other resource groups that must be considered. There are tools like **`ARK`** or now called **`Velero`** by Heptio that can do this for you.

  ![brc](../../images/brc.PNG)

## Backup - ETCD

- So, instead of backing up resources as before, you may choose to backup the ETCD cluster itself.

  ![be](../../images/be.PNG)

- You can take a snapshot of the etcd database by using **`etcdctl`** utility snapshot save command.

  ```
  ETCDCTL_API=3 etcdctl snapshot save snapshot.db
  ```

  ```
  ETCDCTL_API=3 etcdctl snapshot status snapshot.db
  ```

  ![be1](../../images/be1.PNG)

## Restore - ETCD

- To restore etcd from the backup at later in time. First stop kube-apiserver service

  ```
  service kube-apiserver stop
  ```

  or

  ```
  systemctl stop kube-apiserver
  ```

- Run the etcdctl snapshot restore command
- Update the etcd service
- Reload system configs

  ```
  systemctl daemon-reload
  ```

- Restart etcd

  ```
  service etcd restart
  ```

  or

  ```
  service etcd restart
  ```

![er](../../images/er.PNG)
_defining --data-dir creates a new directory. This initializes a new cluster configuration and inits the new membersa as members of the new cluster. This prevents new members accidentally joining an existing cluster._

- Start the kube-apiserver

  ```
  service kube-apiserver start
  ```

  or

  ```
  service kube-apiserver start
  ```

#### With all etcdctl commands specify the cert,key,cacert and endpoint for authentication

```
$ ETCDCTL_API=3 etcdctl \
  snapshot save /tmp/snapshot.db \
  --endpoints=https://[127.0.0.1]:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/etcd-server.crt \
  --key=/etc/kubernetes/pki/etcd/etcd-server.key
```

![erest](../../images/erest.PNG)

### Updating with etcd cluster is often not possible when using managed solutions, since you dont have access to etcd cluster

### Solution to Lab - restore from ETCD

First, using the etcdutl command, restore the snapshot:

etcdutl snapshot restore /opt/snapshot-pre-boot.db --data-dir /var/lib/etcd-from-backup

Example output:

```shell
etcdutl snapshot restore /opt/snapshot-pre-boot.db --data-dir /var/lib/etcd-from-backup`
2025-04-24T09:38:07Z info snapshot/v3_snapshot.go:265 restoring snapshot {"path": "/opt/snapshot-pre-boot.db", "wal-dir": "/var/lib/etcd-from-backup/member/wal", "data-dir": "/var/lib/etcd-from-backup", "snap-dir": "/var/lib/etcd-from-backup/member/snap", "initial-memory-map-size": 10737418240}
2025-04-24T09:38:07Z info membership/store.go:141 Trimming membership information from the backend...
2025-04-24T09:38:07Z info membership/cluster.go:421 added member {"cluster-id": "cdf818194e3a8c32", "local-member-id": "0", "added-peer-id": "8e9e05c52164694d", "added-peer-peer-urls": ["http://localhost:2380"]}
2025-04-24T09:38:07Z info snapshot/v3_snapshot.go:293 restored snapshot {"path": "/opt/snapshot-pre-boot.db", "wal-dir": "/var/lib/etcd-from-backup/member/wal", "data-dir": "/var/lib/etcd-from-backup", "snap-dir": "/var/lib/etcd-from-backup/member/snap", "initial-memory-map-size": 10737418240}
```

Note: In this case, we are restoring the snapshot to a different directory which is in the same server where we took the backup (the controlplane node). As a result, the only required option for the restore command is the --data-dir.

Next, we need to update the /etc/kubernetes/manifests/etcd.yaml to point to the newly restored directory, which is /var/lib/etcd-from-backup. The only change that we need to make to the YAML file, is to change the hostPath for the volume called etcd-data from old directory /var/lib/etcd to the new directory /var/lib/etcd-from-backup:

> Edit: You also need to update the command line argument to container that specifies `data-dir` to use `/var/lib/etcd-from-backup`. You also need to update the property for volumeMounts that mentions /var/lib/etcd to /var/lib/etcd-from-backup
> See <https://discuss.kubernetes.io/t/etcd-backup-and-restore-management/11019/9>

...
volumes:

- hostPath:
  path: /var/lib/etcd-from-backup # Newly restored backup directory
  type: DirectoryOrCreate
  name: etcd-data

With this change, /var/lib/etcd on the container points to /var/lib/etcd-from-backup on the controlplane.

When this file is updated, the ETCD pod is automatically re-created as this is a static pod placed under the /etc/kubernetes/manifests directory. This may take a few minutes, and it is expected that kube-controller-manager and kube-scheduler will also restart. To check the containers being restarted:

`watch crictl ps`

Once the updated etcd container and the kube-apiserver containers are up, you can verify that the missing deployments (2 deployments) and services (3 services) are restored again:

kubectl get deployments,services

#### K8s Reference Docs

- <https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/>
