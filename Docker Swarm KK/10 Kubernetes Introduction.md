# <span id='top'>10 Kubernetes Introduction</span>

<br>

[[☸️Kubernetes Introduction]](#Introduction)  
[[Commands]](#Commands)  
[[🤲Hands-on Example]](#handson)  
[[References]](#ref)

<br>

## <span id='Introduction'>☸️Kubernetes Introduction</span>

[[Top]](#top)

### Pod

**Key difference**

Unlike `docker swarm`, `☸️Kubernetes` does not run containers directly on the worker nodes. Kubernetes wraps the containers inside a virtual block, known as `pods`.

_Pods are the smallest deployable units of computing that you can create and manage in Kubernetes._

_A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. A Pod's contents are always co-located and co-scheduled, and run in a shared context._ ([Kubernetes docs](https://kubernetes.io/docs/concepts/workloads/pods/))

Another layer of abstraction that Kubernetes provides to group together different containers, their network and storage into a virtual container.

<br>

## <span id='Commands'>Commands</span>

[[Top]](#top)

    kubectl create
    kubectl get
    kubectl describe

    kubectl create -f pod-definition.yml
    kubectl get pods
    kubectl describe pods

### `pod-definition.yml`

- `version`: `v1`
- `kind`: `pod` `deployment` `service`
- `metadata`: `name` `labels`
- `specification`: `containers` `ports`

        ----------- pod-definition.yml -----------

        apiVersion: v1

        kind: pod

        metadata:
            name: my-nginx

        spec:
            containers:
                - name: nginx
                  image: nginx
                  ports:
                    - containerPort: 80

Create the definition file.

        kubectl create -f pod-definition.yml

<br>

## <span id='handson'>🤲Hands-on Example</span>

[[Top]](#top)

1. Deploy containers = deploy pods
2. Enable connectivity = create services (ClusterIP)
3. External access = Create services (LoadBalancer)

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
