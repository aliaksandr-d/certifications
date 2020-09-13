# ReplicaSet
A ReplicaSet ensures that a specified number of pod replicas are running at any given time. However, a Deployment is a higher-level concept that manages ReplicaSets and provides declarative updates to Pods along with a lot of other useful features. Therefore, we recommend using Deployments instead of directly using ReplicaSets, unless you require custom update orchestration or don’t require updates at all.

## YAML
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

# Deployment
A Deployment provides declarative updates for Pods and ReplicaSets.
## Kubectl
### Create
```
kubectl run nginx --image=nginx
```
### Create with port, labels
```
kubectl run frontend --replicas=2 --labels=run=load-balancer-example --image=busybox  --port=8080
```
### Expose deployment on with service
```
kubectl expose deployment frontend --type=NodePort --name=frontend-service --port=6262 --target-port=8080
```
### Set serviceaccount
```
kubectl set serviceaccount deployment frontend myuser
```
### Update deployment image
```
kubectl set image deploy nginx nginx=nginx:1.7.9
```
## YAML
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
# PersistentVolume
## Access modes:
- ReadWriteOnce (RWO) – the volume can be mounted as read-write by a single node
- ReadOnlyMany (ROX) – the volume can be mounted read-only by many nodes
- ReadWriteMany (RWX) – the volume can be mounted as read-write by many nodes
## emptyDir
emptyDir are volumes that get created empty when a Pod is created.
While a Pod is running its emptyDir exists. If a container in a Pod crashes the emptyDir content is unaffected. Deleting a Pod deletes all its emptyDirs.
There are several ways a Pod can be deleted. Accidental and deliberate. All result in immediate emptyDir deletion. emptyDir are meant for temporary working disk space.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myvolumes-pod
spec:
  containers:
  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container
    command: ...
    
    volumeMounts:
    - mountPath: /demo
      name: demo-volume
  volumes:
  - name: demo-volume
    emptyDir: {}
 ```
## YAML
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

# PersistentVolumeClaim
## YAML
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
  selector:
    matchLabels:
      type: local
```


# Pod
## Kubectl
### Create
```
kubectl run nginx --image=nginx --restart=Never
```
### Create with request limits
```
kubectl run nginx -image=nginx --restart=Never --port=80 --namespace=myname --command --serviceaccount=mysa1 --env=HOSTNAME=local --labels=bu=finance,env=dev  --requests='cpu=100m,memory=256Mi' --limits='cpu=200m,memory=512Mi' --dry-run -o yaml - /bin/sh -c 'echo hello world'
```
### Change labels
```
kubectl label po nginx2 app=v2 --overwrite
```
### Remove labels for several pods
```
kubectl label po nginx1 nginx2 nginx3 app-
```
### Change annotations
```
kubectl annotate po nginx{1..3} description='my description'
```
### Remove annotations
```
kubectl annotate po nginx{1..3} description-
```
## YAML
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-name
  labels:
    run: nginx
spec: 
  serviceAccountName: mysauser 
  securityContext: # insert this line
    runAsUser: 101 # UID for the user
  volumes: # add a volumes list
  - name: myvolume # just a name, you'll reference this in the pods
    configMap:
      name: cmvolume # name of your configmap
  - name: secretvolume 
    secret: # we want a secret
      secretName: mysecret2
  - name: task-pv-storage
    persistentVolumeClaim:
      claimName: task-pv-claim
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      imagePullPolicy: IfNotPresent
      name: nginx
      resources: {}
          securityContext: # insert this line
            capabilities: # and this
              add: ["NET_ADMIN", "SYS_TIME"] # this as well
      env:
      - name: option # name of the env variable
        valueFrom:
          configMapKeyRef:
            name: options # name of config map
            key: var5 # name of the entity in config map
      envFrom: # different than previous one, that was 'env'
       - configMapRef: 
           name: anotherone # the name of the config map
       volumeMounts: # your volume mounts are listed here
        - name: myvolume # the name that you specified before
          mountPath: /etc/lala # the path inside your container
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
  nodeSelector:
    accelerator: nvidia-tesla-p100
```

# Job
## Kubectl
### Create
```
kubectl run nginx --image=nginx --restart=OnFailure
```
## YAML
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  parallelism: 5 # make job run 5 parallel times
  template: # pod template
    metadata:
      creationTimestamp: null
      labels:
        run: busybox
    spec:
      containers:
      - args:
        - /bin/sh
        - -c
        - echo hello;sleep 30;echo world
        image: busybox
        name: busybox
        resources: {}
      restartPolicy: OnFailure
```

# Cron job
## Kubectl
### Create
```
kubectl run nginx --image=nginx  --restart=OnFailure --schedule="* * * * *"
```
## YAML
```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate: # job template
    spec:
      template: # pod template
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

# Service
Type values and their behaviors are:
* *ClusterIP*: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType.
* *NodePort*: Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>.
* *LoadBalancer*: Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
* *ExternalName*: Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value. No proxying of any kind is set up.
## Kubectl
### Create
```
kubectl create service clusterip my-cs --tcp=5678:8080 --dry-run -o yaml
```
### Expose deployment on with service
```
kubectl expose deployment frontend --type=NodePort --name=frontend-service --port=6262 --target-port=8080
```
## YAML
```yaml
  kind: Service 
apiVersion: v1 
metadata:
  name: hostname-service 
spec:
  # Expose the service on a static port on each node
  # so that we can access the service from outside the cluster 
  type: NodePort

  # When the node receives a request on the static port (30163)
  # "select pods with the label 'app' set to 'echo-hostname'"
  # and forward the request to one of them
  selector:
    app: echo-hostname 

  ports:
    # Three types of ports for a service
    # nodePort - a static port assigned on each the node
    # port - port exposed internally in the cluster
    # targetPort - the container port to send requests to
    - nodePort: 30163
      port: 8080 
      targetPort: 80
```
      

# ConfigMap
## Kubectl
### Create
```
kubectl create configmap config --from-literal=foo=lala --from-literal=foo2=lolo
```

# ServiceAccount
## YAML
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myuser
```

# NetworkPolicy
A network policy specification consists of four elements:

- *podSelector*: the pods that will be subject to this policy (the policy target) - mandatory
- *policyTypes*: specifies which types of policies are included in this policy, ingress and/or egress - this is optional but I recommend to always specify it explicitly.
- *ingress*: allowed inbound traffic to the target pods - optional
- *egress*: allowed outbound traffic from the target pods - optional

## YAML
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector: # pods-subject to this policy same namespace (metadata.namespace)
    matchLabels:
      app: db
  policyTypes: # types of policy
  - Ingress
  - Egress
  ingress: # allowed inbound traffic to pods-subject
  - from: # multiple "from" are allowed with logical OR
    - ipBlock: # allow traffic from subnet
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector: # allow traffic from frontend pods in "myproject" namespace
        matchLabels:
          project: myproject
      podSelector: 
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
    - port: 443
      protocol: TCP
    - port: 80
      protocol: TCP
  egress: #allowed outbound traffic from pod-subjects
  - to:
    - ipBlock: #allow traffic to subnet
        cidr: 10.0.0.0/24
    ports: 
    - protocol: TCP
      port: 5978
  - to: # you must allow access to the K8s DNS service
    - namespaceSelector: {} # allow DNS only inside the cluster
    ports:
    - protocol: UDP
      port: 53
  - to:
    - ipBlock:
        cidr: 8.8.8.8/32 # allow access to Google DNS
    ports:
    - protocol: UDP
      port: 53
```

## YAML - deny all
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

# RBAC

## Role

### YAML
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```
## RoleBinding

### YAML
```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "jane" to read pods in the "default" namespace.
# You need to already have a Role named "pod-reader" in that namespace.
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
# You can specify more than one "subject"
- kind: User
  name: jane # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```


## ClusterRole

### Create
```
kubectl create clusterrole secret --verb="*" --resource=secret
kubectl create clusterrole deploy --verb="*" --resource=pods --resource-name=compute
```

### YAML
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
- apiGroups: [""]
  #
  # at the HTTP level, the name of the resource for accessing Secret
  # objects is "secrets"
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```
## ClusterRoleBinding

### Create
```
kubectl create clusterrolebinding deploy --user=deploy@test.com --clusterrole=deploy
kubectl create clusterrolebinding secret --user=secret@test.com --clusterrole=secret
```

### Check
```
kubectl auth can-i create secret --as secret@test.com 
kubectl auth can-i "*" secret --as secret@test.com
```

### YAML
```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```
