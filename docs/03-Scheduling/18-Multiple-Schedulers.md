# Multiple Schedulers

- Take me to [Video Tutorial](https://kodekloud.com/topic/multiple-schedulers/)

In this section, we will take a look at multiple schedulers

## Custom Schedulers

- Your kubernetes cluster can schedule multiple schedulers at the same time.

  ![ms](../../images/ms.PNG)

## Deploy additional scheduler

- Download the binary

```
wget https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-scheduler
```

- Create a resource definition for custom scheduler

```yaml
# my-scheduler-config.yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: my-scheduler-2
```

![das](../../images/das.PNG)
_no need to define scheduler-name in the commandline if it is already mentioned in the resource definition file._

## Deploy additional scheduler - kubeadm

![dask](../../images/dask.PNG)

- To create a scheduler pod

  ```
  kubectl create -f my-custom-scheduler.yaml
  ```

## Deploy addition scheduler - kubeadm (Version from Video)

![custom-scheduler-kubeadm](assets/custom-scheduler-kubeadm.png)
_leaderElect option: used if you have multiple copies of the scheduler running on multiple master nodes. If multiple copies of the same scheduler are running, you have to chose the leading scheduler._

## View Schedulers

- To list the scheduler pods

  ```
  kubectl get pods -n kube-system
  ```

## Use the Custom Scheduler

- Create a pod definition file and add new section called **`schedulerName`** and specify the name of the new scheduler

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
  spec:
    containers:
    - image: nginx
      name: nginx
    schedulerName: my-custom-scheduler
  ```

  ![cs](../../images/cs.png)

- To create a pod definition

  ```
  kubectl create -f pod-definition.yaml
  ```

- To list pods

  ```
  kubectl get pods
  ```

## View Events to know which scheduler picked up the pod

![view-events](assets/view-events.png)

- To view events

  ```
  kubectl get events
  ```

  ![cs1](../../images/cs1.PNG)

## View Scheduler Logs to check issues

- To view scheduler logs

  ```
  kubectl logs my-custom-scheduler -n kube-system
  ```

  ![cs2](../../images/cs2.PNG)

#### K8s Reference Docs

- <https://kubernetes.io/docs/tasks/extend-kubernetes/configure-multiple-schedulers/>
