# <span id='top'>10 ☸️Kubernetes Bonus Demo</span>

<br>

[[☸️Kubernetes Service]](#service)  
[[☸️k8s Lab – Play with Kubernetes]](#Lab)  
[[References]](#ref)

<br>
 
## <span id='service'>☸️Kubernetes Service</span>

[[Top]](#top)

### `Service`

_In Kubernetes, a Service is an abstraction which defines a logical set of Pods and a policy by which to access them (sometimes this pattern is called a micro-service). The set of Pods targeted by a Service is usually determined by a selector._ ([docs](https://kubernetes.io/docs/concepts/services-networking/service/))

1. Setup a Google Conainter Engine enviornment.
2. Create `☸️k8s` pods.
3. Create Services - ClusterIP - internal
4. Create Services - LoadBalancer - external

<br>

## <span id='Lab'>☸️k8s Lab – Play with Kubernetes</span>

[[Top]](#top)

                                WARNING!!!!

        This is a sandbox environment. Using personal credentials
        is HIGHLY! discouraged. Any consequences of doing so, are
        completely the user's responsibilites.

        You can bootstrap a cluster as follows:

        1. Initializes cluster master node:

        kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16


        2. Initialize cluster networking:

        kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml


        3. (Optional) Create an nginx deployment:

        kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/nginx-app.yaml

                                The PWK team.

<br>

1.  Initializes cluster master node:

        kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16

        Initializing machine ID from random generator.
        I0212 11:18:48.456737     874 version.go:251] remote version is much newer: v1.23.3; falling back to: stable-1.20
        [init] Using Kubernetes version: v1.20.15
        [preflight] Running pre-flight checks
                [WARNING Service-Docker]: docker service is not active, please run 'systemctl start docker.service'
                [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
                [WARNING FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables does not exist
        [preflight] The system verification failed. Printing the output from the verification:
        KERNEL_VERSION: 4.4.0-101-generic
        DOCKER_VERSION: 20.10.1
        OS: Linux
        CGROUPS_CPU: enabled
        CGROUPS_CPUACCT: enabled
        CGROUPS_CPUSET: enabled
        CGROUPS_DEVICES: enabled
        CGROUPS_FREEZER: enabled
        CGROUPS_MEMORY: enabled
        CGROUPS_PIDS: enabled
        CGROUPS_HUGETLB: enabled
                [WARNING SystemVerification]: this Docker version is not on the list of validated versions: 20.10.1. Latest validated version: 19.03
                [WARNING SystemVerification]: failed to parse kernel config: unable to load kernel module: "configs", output: "", err: exit status 1
        [preflight] Pulling images required for setting up a Kubernetes cluster
        [preflight] This might take a minute or two, depending on the speed of your internet connection
        [preflight] You can also perform this action in beforehand using 'kubeadm config images pull'

2.  Initialize cluster networking:

        kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml

        configmap/kube-router-cfg created
        daemonset.apps/kube-router created
        serviceaccount/kube-router created
        clusterrole.rbac.authorization.k8s.io/kube-router created
        clusterrolebinding.rbac.authorization.k8s.io/kube-router created

3.  Clone the [`voting-app`](https://github.com/mmumshad/kubernetes-example-voting-app).

        git clone https://github.com/mmumshad/kubernetes-example-voting-app.git

        Cloning into 'kubernetes-example-voting-app'...
        remote: Enumerating objects: 39, done.
        remote: Counting objects: 100% (19/19), done.
        remote: Compressing objects: 100% (7/7), done.
        remote: Total 39 (delta 13), reused 12 (delta 12), pack-reused 20
        Unpacking objects: 100% (39/39), done.

Now you have the repo on local.

        [node1 kubernetes-example-voting-app]$ ls

        README.md   postgres-service.yml
                    redis-service.yml
                    result-app-service.yml
                    voting-app-service.yml

                    redis-pod.yml
                    postgres-pod.yml
                    result-app-pod.yml
                    voting-app-pod.yml
                    worker-app-pod.yml

4.  Create a `pod`.

        kubectl create -f voting-app-pod.yml

        pod/voting-app-pod created

Check status

        kubectl get pods
        NAME             READY   STATUS    RESTARTS   AGE
        voting-app-pod   0/1     Pending   0          46s

Get more detailed description of `pods`.

        kubectl describe pods

        Name:         voting-app-pod
        Namespace:    default
        Priority:     0
        Node:         <none>
        Labels:       app=demo-voting-app
                    name=voting-app-pod
        Annotations:  <none>
        Status:       Pending
        IP:
        IPs:          <none>

        0/1 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate.  <------------ known issue

Node affinity is a property of Pods that attracts them to a set of nodes (either as a preference or a hard requirement). Taints are the opposite -- they allow a node to repel a set of pods.

        kubectl taint nodes node1 node-role.kubernetes.io/master:NoSchedule-
        node/node1 untainted

By default, `Master node` cannot host any services.

        kubectl create -f voting-app-service.yml
        service/voting-service created

        kubectl get services
        NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
        kubernetes       ClusterIP      10.96.0.1       <none>        443/TCP        10m
        voting-service   LoadBalancer   10.102.28.171   <pending>     80:32546/TCP   18s

Create the `pods` and `services` for the rest of the `yml` files.

        kubectl create -f redis-pod.yml
        pod/redis-pod created

        $ kubectl create -f redis-service.yml
        service/redis created

        $ kubectl create -f postgres-pod.yml
        pod/postgres-pod created

        $ kubectl create -f postgres-service.yml
        service/db created

        $ kubectl create -f worker-app-pod.yml
        pod/worker-app-pod created

        $ kubectl create -f result-app-pod.yml
        pod/result-app-pod created

        $ kubectl create -f result-app-service.yml
        service/result-service created

Now check:

        kubectl get pods
        NAME             READY   STATUS    RESTARTS   AGE
        postgres-pod     0/1     Evicted   0          2m54s  <------------
        redis-pod        0/1     Evicted   0          3m10s  <------------
        result-app-pod   0/1     Pending   0          2m20s
        voting-app-pod   0/1     Evicted   0          13m    <------------
        worker-app-pod   0/1     Pending   0          2m36s

_Eviction is the process of proactively terminating one or more Pods on resource-starved Nodes. In Kubernetes, scheduling refers to making sure that Pods are matched to Nodes so that the kubelet can run them. ... Eviction is the process of terminating one or more Pods on Nodes._

<br>

### Reset.

Delete all the services all at once.

        kubectl delete -f .

        pod "postgres-pod" deleted
        service "db" deleted
        pod "redis-pod" deleted
        service "redis" deleted
        pod "result-app-pod" deleted
        service "result-service" deleted
        pod "voting-app-pod" deleted
        service "voting-service" deleted
        pod "worker-app-pod" deleted

Double check.

        kubectl get pods
        No resources found in default namespace.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
- Mumshad Manambeth (2018) kubernetes-example-voting-app https://github.com/mmumshad/kubernetes-example-voting-app
- No nodes are available that match all of the following predicates:: PodFitsHostPorts (1), PodToleratesNodeTaints (1). #49440 https://github.com/kubernetes/kubernetes/issues/49440
- Kubernetes docs (n.d.) Service https://kubernetes.io/docs/concepts/services-networking/service/
