## KUBERNETES KUSTOMIZE

SOURCE - [Official Documentation](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)

kustomize lets you customize raw, template-free YAML files for multiple purposes, leaving the original YAML untouched and usable as is.
### What I Understand
If you have some understanding about how helm charts work then kustomize is just a younger brother of it. In helm there is a file called values.yaml where you define variables in yaml format and then populate your manifest files using that. Example -
```
# values.yaml
deployment_name: webapp
resources:
  limits:
    cpus: 2
    memory: 128mb
replicas: 3
---
# deployment.yaml

kind: Deployment
metadata: 
  name: {{ .Values.deployment_name | default "demo_deployment" }} # if deployment_name doesn't exists, it will pickup the default value
sepc:
  replicas: {{ .Values.replicas }}
  containers:
    name:
    image:
    resources:
      limits: 
        cpus: {{ .Values.resources.limits.cpus }}
        memory: {{ .Values.resources.limits.memory }}
```

Kustomize does the same, populate the template manifest files. The differences between helm and kustomize are

| Helm | Kustomize |
| -------- | ------- |
| Need to install separately | comes with kubectl |
| It does more than just templating | Used for templating and applying patches on the templates |
| values get called using .Values keyword | it checks against the keyword, no need to introduce any kind of new keyword |

### Getting Started
- create `kustomization.yaml` where your manifests are present.
- This file contains 
  - target manifests known as resources
  - config map and secrets generators
  - patches(if any)

**Generating Resources**
- [Generating Resources](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#generating-resources)
  - It can generate resources such as config map & secrets from files and literals

```
cat <<EOF >plain_text_variable_file.txt
MYSQL_USER=goku
MYSQL_PASSWORD=jiren
EOF

cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - plain_text_variable_file.txt
EOF
```
- Created a directory called prod and then created the mentioned file

```
prod/
├── kustomization.yaml
└── plain_text_variable_file.txt

0 directories, 2 files
```
- Ran the `kubectl kustomize prod/`
```
naveen@hydra:~k8s_charts$ kubectl kustomize prod/
apiVersion: v1
data:
  plain_text_variable_file.txt: |
    MYSQL_USER=goku
    MYSQL_PASSWORD=jiren
kind: ConfigMap
metadata:
  name: example-configmap-1-7ck7bfmbc5
```
- [Similarly a secret can be generated](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#generating-resources)

> Referencing these value to our manifest file
- Now, a deployment yaml need to retrieve the above values we created via kustomize
- It is similar to what we do to normal config map and secrets
```
spec:
      containers:
      - name: app
        image: my-app
        volumeMounts:
        - name: config
          mountPath: /config
      volumes:
      - name: config
        configMap:
          name: example-configmap-1 # the name is same as the generated config map
```
- But here's the catch it might fail to populate as kustomize doesn't know about our deployment yaml. For this `kustomization.yaml` needs to updated
  - a new list named `resources` needs to be introduced
```
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - plain_text_variable_file.txt
resources:
  - deployment.yaml
```
**Setting cross-cutting fields**
- [Setting cross-cutting fields](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#setting-cross-cutting-fields)
- This one shows the true nature of kustomize
- Let's check this example. I have defined a deployment.yaml and you can notice there is no extra variables that let you replace it with values.
  - No namepsace defined in the template
```
cat <<EOF >./deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
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
        image: nginx
EOF
```
- Here's the `kustomization.yaml`
```
cat <<EOF >./kustomization.yaml
namespace: my-namespace # this will check against namespace key.
namePrefix: dev-
nameSuffix: "-001"
commonLabels:
  app: bingo
commonAnnotations:
  oncallPager: 800-555-1212
resources:
- deployment.yaml
EOF
```
- Here's the beautiful output of the running deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    oncallPager: 800-555-1212 # in template it was there but kustomize introduced it in running deployment due to commonAnnotations
  labels:
    app: bingo # this got updated 
  name: dev-nginx-deployment-001 # the name has now prefix and suffix according to defined values in kustomize
  namespace: my-namespace # this wasn't there in template
spec:
  selector:
    matchLabels:
      app: bingo # this one too get updated
  template:
    metadata:
      annotations:
        oncallPager: 800-555-1212 # introduced
      labels:
        app: bingo # updated
    spec:
      containers:
      - image: nginx
        name: nginx
```
**Patching**
- Here's comes another useful usecase of kustomization - [Customization](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#customizing)

> What is patching/customizing?
Patching refers to changing value of running/launching kind's specification. Suppose a deployment has replica set to 2 and now we need 3, it can be using `kubectl apply` but we are using kustomize to create deployment. To fulfill this, patching is needed. How to create it?

Customizing offer two method to patch
- **patchesStrategicMerge:** - This is something I cannot explain. Summary - Not all Resources or fields support strategic merge patches.
- **patchesJson6902:** - To support modifying arbitrary fields in arbitrary Resources, Kustomize offers applying JSON patch through patchesJson6902. To find the correct Resource for a Json patch, the group, version, kind and name of that Resource need to be specified in `kustomization.yaml`.

> Example
```
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF
```
- `kustomization.yaml`
```
cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml

patchesJson6902:
- target: # whatever resources match this condition will have the patch applied. This target says deployment/my-nginx in default namespace.
    group: apps
    version: v1
    kind: Deployment
    name: my-nginx
  path: patch.yaml # this patch is going to be applied
EOF
```
- Here's comes the patch
```
cat <<EOF > patch.yaml
- op: replace # which operation to perform
  path: /spec/replicas # which spec
  value: 3 # updated value
EOF
```