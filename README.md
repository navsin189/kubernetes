## HELM CHART

Helm is widely known as "the package manager for Kubernetes". Although it presents itself like this, its scope goes way beyond that of a simple package manager. However, let's start at the beginning.

### INSTALLATION
> https://helm.sh/docs/intro/install/

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
/get_helm.sh

# to confirm 
which helm
helm version
```

### DEMO

This directory is generated using `helm create k8s_charts` and here's the structure
```
k8s_charts/
├── charts
├── Chart.yaml
├── README.md # this is created by me
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml

4 directories, 12 files

```

To check the status of a helm chart, run `helm show all <chart_directory_name>`. Ex - `helm show all k8s_charts`

#### Key Points
Source - [FreeCodeCamp](https://www.freecodecamp.org/news/what-is-a-helm-chart-tutorial-for-kubernetes-beginners/)

- **chart.yaml:** This is where you'll put the information related to your chart. That includes the chart version, name, and description so you can find it if you publish it on an open repository. Also in this file you'll be able to set external dependencies using the dependencies key.
- **templates (dir):** This is the place where you'll put all your manifest files. All values are filled while calling it from any other manifest files such as deployment.
- **charts:** If your chart depends on another chart you own, or if you don't want to rely on Helm's default library (the default registry where Helm pull charts from), you can bring this same structure inside this directory. Chart dependencies are installed from the bottom to the top, which means if chart A depends on chart B, and B depends on C, the installation order will be C ->B ->A.
- **values.yaml:** Here you can define the values for the variables used in templates.

#### Notes
- You can create a directory and populate with **Chart.yaml** and **values.yaml** *(if needed)* and helm will recognize it and show the details using the above command.