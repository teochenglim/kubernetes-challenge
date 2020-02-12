0. Intro

I am using MacOS, so might be a little bit different than yours.

I used minikube in previous company but than now use Docker for Desktop on MacOS more now.

https://github.com/vagmi/kubernetes-challenge looks okay to me but i still need to modify a little bit to my liking.

Since I am linking github and docker hub account and allow autobuild trigger, the image will be build each time i do git commit.

1. Install docker and turn on Kubernetes

https://docs.docker.com/docker-for-mac/


2. Basic installation of tools (git, kubectl, helm 3)

```shell
## install homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
## use homebrew to install tools
brew install git kubernetes-cli helm jq

```

3. download my repo and change to the git root directory

```shell
git clone https://github.com/teochenglim/kubernetes-challenge teochenglim
cd teochenglim

```

4. Install helm for nginx ingress (if you don't have ingress yet)

```shell
helm init
helm del --purge nginx
helm install nginx stable/nginx-ingress \
    --set controller.service.type="NodePort" \
    --set controller.service.nodePorts.http=30080 \
    --set controller.service.nodePorts.https=30443

```

5. Install using batch file for kubectl apply

```shell
$ ./deploy-k8s.sh

```

6. It is easier using nodePort on Mac

```shell
### Solution 1
$ kubectl get svc -l name=k8schallenge
NAME           TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
k8schallenge   NodePort   10.110.98.232   <none>        80:30971/TCP   21m

$ curl localhost:30971/
Hello Cheng Lim!

### Solution 2 - 1 liner version

$ curl localhost:$(kubectl get svc -l name=k8schallenge -o json | jq -r '.items[0].spec.ports[0].nodePort')
Hello Cheng Lim!

### Solution 3

$ kubectl get pod -l name=k8schallenge
NAME                            READY   STATUS    RESTARTS   AGE
k8schallenge-6f4d8c5747-gqkpz   1/1     Running   0          27m
$ kubectl port-forward k8schallenge-6f4d8c5747-gqkpz 4000:4000
Forwarding from 127.0.0.1:4000 -> 4000
Forwarding from [::1]:4000 -> 4000
#### On another Terminal
$ curl localhost:4000/
Hello Cheng Lim!

```

7. To clean up

```shell
helm del --purge nginx
kubectl delete -f k8s/

```
