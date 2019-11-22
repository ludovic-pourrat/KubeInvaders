# KubeInvaders

*KubeInvaders is a gamified chaos engineering tool for Kubernetes. It is like Space Invaders but the aliens are PODs*

![Alt Text](https://github.com/lucky-sideburn/KubeInvaders/blob/master/images/kubeinvaders.png)

### Description

KubeInvaders has been developed using Defold (https://www.defold.com/).

Through KubeInvaders you can stress your Openshift cluster in a fun way and check how it is resilient.

```
# Please set target_namespace to set your target namespace!
helm install --set-string target_namespace="namespace1\,namespace2" --name kubeinvaders --namespace kubeinvaders ./helm-charts/kubeinvaders
```

### Special Input Keys

| Input           | Action                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------|
|     n           | Change namespace (you should define namespaces list. Ex: TARGET_NAMESPACE=foo1,foo2,foo3).|
|     a           | Switch to automatic mode.                                                                 |
|     m           | Switch to manual mode.                                                                    |
|     h           | Show special keys.                                                                        |
|     q           | Hide help for special keys.                                                               |
|     i           | Show pod's name. Move the ship towards an alien.                                          |


### Environment Variables - Make the game more difficult to win!

Set the following variables in Kubernetes Deployment or Openshift DeploymentConfig

| ENV Var                     | Description                                                                   |
|-----------------------------|-------------------------------------------------------------------------------|
| ALIENPROXIMITY (default 15) | Reduce the value to increase distance between aliens                          |
| HITSLIMIT (default 0)       | Seconds of CPU time to wait before shooting                                   |
| UPDATETIME (default 1)      | Seconds to wait before update PODs status (you can set also 0.x Es: 0.5)      |


### Install with HELM!

```
# Please set target_namespace to set your target namespace!
helm install --set-string target_namespace="namespace1\,namespace2" --name kubeinvaders --namespace kubeinvaders ./helm-charts/kubeinvaders
```

### How the configuration of KubeInvaders DeploymentConfig should be (remember to use your TARGET_NAMESPACE and ROUTE_HOST)

![Alt Text](https://github.com/lucky-sideburn/KubeInvaders/blob/master/images/dcenv.png)

### Install KubeInvaders on Kubernetes

```bash
#Change with the namespace you want to stress
TARGET_NAMESPACE='foobar'
## You can define multiple namespaces ex: TARGET_NAMESPACE=foobar,foobar2

#Change with the URL of your Kubeinvaders
ROUTE_HOST=kubeinvaders.org

kubectl apply -f kubernetes/kubeinvaders-namespace.yml
kubectl apply -f kubernetes/kubeinvaders-deployment.yml -n kubeinvaders
kubectl expose deployment  kubeinvaders --type=NodePort --name=kubeinvaders -n kubeinvaders --port 8080
kubectl apply -f kubernetes/kubeinvaders-ingress.yml -n kubeinvaders
kubectl create sa kubeinvaders -n foobar
kubectl apply -f kubernetes/kubeinvaders-role.yml
kubectl apply -f kubernetes/kubeinvaders-rolebinding.yml

TOKEN=`kubectl describe secret $(kubectl get secret -n foobar | grep 'kubeinvaders-token' | awk '{ print $1}') -n foobar | grep 'token:' | awk '{ print $2}'`

kubectl set env deployment/kubeinvaders TOKEN=$TOKEN -n kubeinvaders
kubectl set env deployment/kubeinvaders NAMESPACE=$TARGET_NAMESPACE -n kubeinvaders
kubectl set env deployment/kubeinvaders ROUTE_HOST=$ROUTE_HOST -n kubeinvaders
```
