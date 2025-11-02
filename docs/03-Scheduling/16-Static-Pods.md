# Static Pods

- Take me to [Video Tutorial](https://kodekloud.com/topic/static-pods/)

In this section, we will take a look at Static Pods

#### How do you provide a pod definition file to the kubelet without a kube-apiserver?

- Problem Statement: kubelet depends on kube-apiserver for intructions on what pods to load on its node. Instructions are stored on the etcd data store.
  - what if there is no kube-apiserver, no etcd-server, etc. - What if the kubelet is the only thing?
- You can configure the kubelet to read the pod definition files from a directory on the server designated to store information about pods.

## Configure Static Pod

- The designated directory can be any directory on the host and the location of that directory is passed in to the kubelet as an option while running the service.
  - The option is named as **`--pod-manifest-path`**.

  ![sp](../../images/sp.PNG)

## Another way to configure static pod

- Instead of specifying the option directly in the **`kubelet.service`** file, you could provide a path to another config file using the config option, and define the directory path as staticPodPath in the file.
- clusters setup by the kubeadm tools use this approach

  ![sp1](../../images/sp1.PNG)

## How to find what static Pods are defined on a cluster

- inspect options passed in file kubelet.service. Could be `--pod-manifest-path` or `--config`
- Identify which node the static pod is created on, ssh to the node. If you don't know the IP of the node, run the kubectl get nodes -o wide command and identify the IP. Then SSH to the node using that IP. For static pod manifest path look at the file **`/var/lib/kubelet/config.yaml`** on node.

  ```shell
  kubectl get pods -o wide
  kubectl get nodes -o wide
  ssh <ip address of pod>
  grep staticPodPath /var/lib/kubelet/config.yaml
  node01 $ rm -rf /etc/just-to-mess-with-you/greenbox.yaml
  ```

## How to identify if a Pod is a static Pod

Look at the field ownerReference in `kubectl get <pod pod-name> -o yaml`. If the owner is a Node, then it's a static pod.

## View the static pods

- To view the static pods

  ```
  docker ps
  ```

  Remember: `kubectl get pods` cannot be used because it depends on the kube-apiserver, which does not exist.

  ![sp2](../../images/sp2.PNG)

## Fi

#### The kubelet can create both kinds of pods - the static pods and the ones from the api server at the same time

The kube-apiserver is aware of the static Pods as well.
When kubelet creates static pod, it also creates a mirror object on the kube-apiserver.

If you are on a cluster that has a controlplane, you can use `kubectl get pods` and look for Pods that have a node-name appended in their name. This happens automatically.
![sp3](../../images/sp3.PNG)

## Static Pods - Use Case

Since static Pods don't depend on the Kubernetes Controlplane, you can create components of the Controlplane itself as static pods on a node.
Just install kubelet on the master nodes and create the definition yamls for apiserver, controller, etcd, etc.
Put those files in the deisgnated manifest directory and kubelet automatically creates the required pods.
This way, you don't need to download any binaries and configure them.
This is how kubeadm does it.
![sp4](../../images/sp4.PNG)

![sp5](../../images/sp5.PNG)

## Static Pods vs DaemonSets

![spvsds](../../images/spvsds.PNG)

#### K8s Reference Docs

- <https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/>
