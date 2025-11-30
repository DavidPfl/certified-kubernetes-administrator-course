# TLS in Kubernetes

- Take me to [Video Tutorial](https://kodekloud.com/topic/tls-in-kubernetes/)

In this section, we will take a look at TLS in kubernetes

#### The two primary requirements are to have all the various services within the cluster to use server certificates and all clients to use client certificates to verify they are who they say they are

- Server Certificates for Servers
- Client Certificates for Clients

  ![tls](../../images/tls.PNG)

#### Let's look at the different components within the k8s cluster and identify the various servers and clients and who talks to whom

![certs](../../images/certs.PNG)
_kube-apiserver is the only one who talks to the etcd-server. From the POV of etcd-server, the kube-apiserver is a client. So kube-apiserver also needs client certificate as well. kube-apiserver also talks to the kubelet-servers on the worker nodes, to it also needs client cert for kubelet-servers_
