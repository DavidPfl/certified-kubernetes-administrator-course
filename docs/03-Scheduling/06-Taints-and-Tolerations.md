# Taints and Tolerations

- Take me to [Video Tutorial](https://kodekloud.com/topic/taints-and-tolerations-2/)

In this section, we will take a look at taints and tolerations.

- Pod to node relationship and how you can restrict what pods are placed on what nodes.

#### Taints and Tolerations are used to set restrictions on what pods can be scheduled on a node

- Only pods which are tolerant to the particular taint on a node will get scheduled on that node.
- By default, Pods have no Tolerations, so a Pod cannot be assigned to a tainted Node.

  ![tandt](../../images/tandt.PNG)
  _here, Node 1 has a blue taint. Pod D gets a toleration for blue and thus is the only Pod that can get deployed on Node 1._

## Taints

- Use **`kubectl taint nodes`** command to taint a node.

  Syntax

  ```
  kubectl taint nodes <node-name> key=value:taint-effect
  ```

  Example

  ```
  kubectl taint nodes node1 app=blue:NoSchedule
  ```

- The taint effect defines what would happen to the pods if they do not tolerate the taint.
- There are 3 taint effects
  - **`NoSchedule`**: Pod is not scheduled on the node
  - **`PreferNoSchedule`**: System tries to avoid node, but it's not guaranteed
  - **`NoExecute`**: Pod is not scheduled on node and existing Pods on the Node are evicted if they do not tolerate the taint (good for removing previously added Pods, before Node was tainted)

  ![tn](../../images/tn.PNG)

## Tolerations

- Tolerations are added to pods by adding a **`tolerations`** section in pod definition.

  ```
  apiVersion: v1
  kind: Pod
  metadata:
   name: myapp-pod
  spec:
   containers:
   - name: nginx-container
     image: nginx
   tolerations:
   - key: "app"
     operator: "Equal"
     value: "blue"
     effect: "NoSchedule"
  ```

![tp](../../images/tp.PNG)

#### Taints and Tolerations do not tell the pod to go to a particular node. Instead, they tell the node to only accept pods with certain tolerations

If you want to restrict a Pod to certain nodes, you need to use a concept called Node affinity.

The MasterNode is a node like any other node, capable of running Pods. But no Pod gets scheduled to the MasterNode. This is due to a Taint on the MasterNode.
To see this taint, run the below command

```
kubectl describe node kubemaster |grep Taint
```

![tntm](../../images/tntm.PNG)

#### K8s Reference Docs

- <https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/>
