# Cluster Upgrade Introduction

- Take me to [Video Tutorial](https://kodekloud.com/topic/cluster-upgrade-introduction/)

#### Is it mandatory for all of the kubernetes components to have the same versions?

- No, The components can be at different release versions.

#### At any time, kubernetes supports only up to the recent 3 minor versions

- The recommended approach is to upgrade one minor version at a time.

  ![up2](../../images/up2.PNG)

#### Options to upgrade k8s cluster

![opt](../../images/opt.PNG)

## Upgrading a Cluster

- Upgrading a cluster involves 2 major steps

#### There are different strategies that are available to upgrade the worker nodes

- One is to upgrade all at once. But then your pods will be down and users will not be able to access the applications.
  ![stg1](../../images/stg1.PNG)
- Second one is to upgrade one node at a time.
  ![stg2](../../images/stg2.PNG)
- Third one would be to add new nodes to the cluster
  ![stg3](../../images/stg3.PNG)

## kubeadm - Upgrade master node

- kubeadm has an upgrade command that helps in upgrading clusters.

  ```
  kubeadm upgrade plan
  ```

  ![kube1](../../images/kube1.png)

- Upgrade kubeadm from v1.11 to v1.12

  ```
  apt-get upgrade -y kubeadm=1.12.0-00
  ```

- Upgrade the cluster

  ```
  kubeadm upgrade apply v1.12.0
  ```

- If you run the 'kubectl get nodes' command, you will see the older version. This is because in the output of the command it is showing the versions of kubelets on each of these nodes registered with the API Server and not the version of API Server itself

  ```
  kubectl get nodes
  ```

  ![kubeu](../../images/kubeu.PNG)

- Upgrade 'kubelet' on the master node

  ```
  apt-get upgrade kubelet=1.12.0-00
  ```

- Restart the kubelet

  ```
  systemctl restart kubelet
  ```

- Run 'kubectl get nodes' to verify

  ```
  kubectl get nodes
  ```

  ![kubeu1](../../images/kubeu1.PNG)

## kubeadm - Upgrade worker nodes

- From master node, run 'kubectl drain' command to move the workloads to other nodes

  ```
  kubectl drain node-1
  ```

- Upgrade kubeadm and kubelet packages

  ```
  apt-get upgrade -y kubeadm=1.12.0-00
  apt-get upgrade -y kubelet=1.12.0-00
  ```

- Update the node configuration for the new kubelet version

  ```
  kubeadm upgrade node config --kubelet-version v1.12.0
  ```

- Restart the kubelet service

  ```
  systemctl restart kubelet
  ```

- Mark the node back to schedulable

  ```
  kubectl uncordon node-1
  ```

  ![kubeu2](../../images/kubeu2.PNG)

- Upgrade all worker nodes in the same way

  ![kubeu3](../../images/kubeu3.PNG)

### Copied from solutions

To seamlessly transition from Kubernetes v1.33 to v1.34 and gain access to the packages specific to the desired Kubernetes minor version, follow these essential steps during the upgrade process. This ensures that your environment is appropriately configured and aligned with the features and improvements introduced in Kubernetes v1.33.

On the controlplane node:

Use any text editor you prefer to open the file that defines the Kubernetes apt repository.

`vim /etc/apt/sources.list.d/kubernetes.list`

Update the version in the URL to the next available minor release, i.e v1.34.

deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] <https://pkgs.k8s.io/core:/stable:/v1.34/deb/> /

After making changes, save the file and exit from your text editor. Proceed with the next instruction.

`apt update`

`apt-cache madison kubeadm`

Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.34.0, the available package version is 1.34.0-1.1. Therefore, to install kubeadm for Kubernetes v1.34.0, use the following command:

`apt-get install kubeadm=1.34.0-1.1`

Run the following command to upgrade the Kubernetes cluster.

`kubeadm upgrade plan v1.34.0`

`kubeadm upgrade apply v1.34.0`

    Note that the above steps can take a few minutes to complete.

Now, upgrade the Kubelet version. Also, mark the node (in this case, the "controlplane" node) as schedulable.

`apt-get install kubelet=1.34.0-1.1`

Run the following commands to refresh the systemd configuration and apply changes to the Kubelet service:

`systemctl daemon-reload`

`systemctl restart kubelet`

#### Demo Video on [Cluster Upgrade](https://kodekloud.com/topic/demo-cluster-upgrade/)

#### K8s Reference Docs

- <https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/>
- <https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-upgrade/>
