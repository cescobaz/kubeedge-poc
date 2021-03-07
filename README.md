# KubeEdge POC

This repository contains configuration file to set-up a simple KubeEdge environment.  
Files are copied and edited from the official [KubeEdge repository](https://github.com/kubeedge/kubeedge).  

##  Deploy the cloud part into a k8s cluster

This method will guide you to deploy the cloud part into a k8s cluster,
so you need to login to the k8s master node (or where else if you can
operate the cluster with `kubectl`).

The manifests and scripts in `github.com/kubeedge/kubeedge/build/cloud`
will be used, so place these files to somewhere you can kubectl with.

### Prepare cloud image
Ensure your k8s cluster can pull edge controller image. If the
image not exist. We can make one, and push to your registry.

```bash
cd $GOPATH/src/github.com/kubeedge/kubeedge
make cloudimage
```

If you are using `minikube` you can build into minikube registry from your shell running:
```bash
eval $(minikube docker-env)
```

### Update config

* `manifests/05-configmap.yaml` set `advertiseAddress` to cluster IP.
* `manifests/07-deployment.yaml` note the image name and check that cluster can pull the image. See previous chapter.
* Based on `manifests/08-service.yaml` and `manifests/09-service-load-balancer.yaml`, create your own service and load balancer to expose cloud hub to outside of k8s cluster, so that edge core can connect to.

Also check the content of each manifest to make sure it meets your environment.

### Create cloud resources
Create k8s resources from the manifests in name order.

```bash
for resource in $(ls *.yaml); do kubectl create -f $resource; done
```
