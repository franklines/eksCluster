# eksCluster - AWS EKS Deployment Jenkins Job

![img](https://i.imgur.com/WbhyVCK.png)

## About
This repository houses a simple Jenkins Job that will deploy an AWS EKS cluster using your defined YAML cluster spec file (`eks/cluster.yml`). It uses the `eksctl` utility to accomplish this, you can learn more about the tool here https://eksctl.io/. Average deployment times have ranged from ~**23-25 mins** (The AWS EKS Control Plane takes a bit to finish setting up :sweat:).

**NOTE:** After you fork/copy this repository, **DO NOT forget to UPDATE the** `eks.pub` public key in the `eks/` directory with **YOUR own public key**!

## How to Use
![img](https://i.imgur.com/wJWmjJ4.png)
You basically just need to add this job (after you've forked/copy it into your own repo) to your Jenkins server (can be local) and hit the `Build with Parameters` icon, enter your AWS credentials, then hit build. :flushed:

```
Login to Jenkins Server >> New Item (top left) >> Enter a name for the job ("eksCluster") >> Click Multibranch Pipeline >> Hit Apply >> "Branch Sources" select Github and enter your specific repos URL (ex: https://github.com/franklines/eksCluster) >> Hit Save/Apply >> Select your branch >> Build with Parameters
```

**NOTE:** Use the commands below to deploy a quick local dev/test Jenkins server (if needed).
```
# https://www.jenkins.io/doc/book/installing/docker/
docker network create jenkins

docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 docker:dind
  
docker run --name jenkins-blueocean --rm --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkinsci/blueocean:latest
```

## After Cluster Deployment
#### Download/Install kubectl to interact with the cluster
```
# On Mac
brew install kubectl

# Linux, etc.
https://kubernetes.io/docs/tasks/tools/install-kubectl/
```

#### Export your cluster's kubeconfig
```
aws eks --profile=<profile> --region <region> update-kubeconfig --name <cluster name>
```

#### Test kubectl w/ command
```
➜  ~ kubectl get nodes -o wide
NAME                             STATUS   ROLES    AGE   VERSION              INTERNAL-IP      EXTERNAL-IP     OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-192-168-0-78.ec2.internal     Ready    <none>   32m   v1.18.9-eks-d1db3c   192.168.0.78     100.27.17.221   Amazon Linux 2   4.14.203-156.332.amzn2.x86_64   docker://19.3.6
ip-192-168-39-119.ec2.internal   Ready    <none>   32m   v1.18.9-eks-d1db3c   192.168.39.119   18.206.124.73   Amazon Linux 2   4.14.203-156.332.amzn2.x86_64   docker://19.3.6
```

