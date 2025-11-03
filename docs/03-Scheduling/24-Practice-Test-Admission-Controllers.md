# Practice Test Admission Controllers

2. Which admission controller is not enabled by default?

   `kubectl exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep 'enable-admission-plugins'`

3. Which admission controller is enabled in this cluster which is normally disabled?

   `cat /etc/kubernetes/manifests/kube-apiserver.yaml`

   or

   `grep enable-admission-plugins /etc/kiubermetes/manifests/kube-apiserver.yaml`
   --> NodeRestriction

4. Enable the NamespaceAutoProvision admission controller

   `vi /etc/kubernetes/manifests/kube-apiserver.yaml`
   and then append `NamespaceAutoProvision` to the line with `- --enable-admission-plugins`

5. Disable `DefaulStorageClass` admission controller
   Edit the `/etc/kubernetes/manifests/kube-apiserver.yaml` and add `- --disable-admission-plugins=DefaultStorageClass`
