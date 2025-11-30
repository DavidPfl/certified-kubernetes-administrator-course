# Authorization

- Take me to [Video Tutorial](https://kodekloud.com/topic/authorization/)

In this section, we will take a look at authorization in kubernetes

## Why do you need Authorization in your cluster?

- As an admin, you can do all operations

  ```
  kubectl get nodes
  kubectl get pods
  kubectl delete node worker-2
  ```

  ![at1](../../images/at1.PNG)

## Authorization Mechanisms

- There are different authorization mechanisms supported by kubernetes
  - Node Authorization
  - Attribute-based Authorization (ABAC)
  - Role-Based Authorization (RBAC)
  - Webhook

## Node Authorization - access within the cluster

![node-auth](../../images/node-auth.png)

## ABAC - assign users or usergroups a set of permissions

![abac](../../images/abac.PNG)

- Policies are created in a policy-file and passed to the kube-apiserver.

- Whenever you want to add or change a policy, you need to manually change this file and restart the kube-apiserver.

- This makes ABAC difficult to manage

## RBAC

- define roles and give the role permissions.
- then, assign users a role.

![rbac](../../images/rbac.PNG)

## Webhook

![webhook](../../images/webhook.PNG)

## Authorization Modes

- The mode options can be defined on the kube-apiserver
- if not specified, default is **AlwaysAllow**

  ![mode](../../images/mode.PNG)

- When you specify multiple modes, it will authorize in the order in which it is specified

  ![mode1](../../images/mode1.PNG)

- Everytime a module denies a request, it goes to the next in the chain
- As soon as one module approves the request, no more authorization is needed and the user gets access to the requested resource

  #### K8s Reference Docs

  - <https://kubernetes.io/docs/reference/access-authn-authz/authorization/>
