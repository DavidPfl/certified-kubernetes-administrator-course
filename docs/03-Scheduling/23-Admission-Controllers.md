# Admission Controllers

Everytime we send a request, the request goes to the kube-apiserver, gets through authentication, authorization, an action is performed and relevant data is stored to the etcd datastore.

![authentication-authorization](assets/authentication-authorization.png)

Authorization based on roles can controll what the user is allowed to do, for example:

![authorization](assets/authorization.png)

Sometime you might want to achieve more fine grained control (like "a request must not create a new namespace"). This is where Admission Controllers come in.

![admission-controller](assets/admission-controller.png)
_The NamespaceExists controller is enabled by default an prevents requests to create a namespace. If you want to change that, you can enable NamespaceAutoProvision in the Admission Controller._

## Viewing enabled Admission Controllers

`kube-apiserver -h | grep enable-admission-plugins`

## Enabling/Disabling Admission Controllers

![enable-disable-admission-controller](assets/enable-disable-admission-controller.png)
