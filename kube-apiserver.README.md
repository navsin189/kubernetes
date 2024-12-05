## Connect to API via Script
Kube-APIServer expose APIs which users call via kubectl. So, instead of kubectl I need to call the API directly.

### How?
- API list can be found on official website.
- But they need authorization that is a JWT token.
- `curl -H "Authorization: Bearer eyJhbGc.............TlI2Aw" -k https://127.0.0.1:6443/api/v1/namespaces/default/pods`
```
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "62176"
  },
  "items": [
    {
      "metadata": {
        "name": "label-demo",
        "namespace": "default",
        "uid": "f9ad709e-e400-49a1-b218-04f1737f0a1e",
        "resourceVersion": "62170",
        "creationTimestamp": "2024-12-04T04:36:59Z",
        "labels": {
          "app": "nginx",
          "environment": "production"
        },
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"nginx\",\"environment\":\"production\"},\"name\":\"label-demo\",\"namespace\":\"default\"},\"spec\":{\"containers\":[{\"image\":\"nginx:1.14.2\",\"name\":\"nginx\",\"ports\":[{\"containerPort\":80}],\"resources\":{\"limits\":{\"memory\":\"512M\"}}}]}}\n"
        },
        "managedFields": [
          {
            "manager": "kubectl-client-side-apply",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2024-12-04T04:36:59Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:annotations": {
                  ".": {},
                  "f:kubectl.kubernetes.io/last-applied-configuration": {}
                },
                "f:labels": {
                  ".": {},
                  "f:app": {},
                  "f:environment": {}
                }
              },
              "f:spec": {
                "f:containers": {
                  "k:{\"name\":\"nginx\"}": {
                    ".": {},
                    "f:image": {},
                    "f:imagePullPolicy": {},
                    "f:name": {},
                    "f:ports": {
                      ".": {},
                      "k:{\"containerPort\":80,\"protocol\":\"TCP\"}": {
                        ".": {},
                        "f:containerPort": {},
                        "f:protocol": {}
                      }
                    },
                    "f:resources": {
                      ".": {},
                      "f:limits": {
                        ".": {},
                        "f:memory": {}
                      },
                      "f:requests": {
                        ".": {},
                        "f:memory": {}
                      }
                    },
                    "f:terminationMessagePath": {},
                    "f:terminationMessagePolicy": {}
                  }
                },
                "f:dnsPolicy": {},
                "f:enableServiceLinks": {},
                "f:restartPolicy": {},
                "f:schedulerName": {},
                "f:securityContext": {},
                "f:terminationGracePeriodSeconds": {}
              }
            }
          },
          {
            "manager": "k3s",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2024-12-04T04:37:00Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:status": {
                "f:conditions": {
                  "k:{\"type\":\"ContainersReady\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  },
                  "k:{\"type\":\"Initialized\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  },
                  "k:{\"type\":\"PodReadyToStartContainers\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  },
                  "k:{\"type\":\"Ready\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  }
                },
                "f:containerStatuses": {},
                "f:hostIP": {},
                "f:hostIPs": {},
                "f:phase": {},
                "f:podIP": {},
                "f:podIPs": {
                  ".": {},
                  "k:{\"ip\":\"10.42.1.25\"}": {
                    ".": {},
                    "f:ip": {}
                  }
                },
                "f:startTime": {}
              }
            },
            "subresource": "status"
          }
        ]
      },
      "spec": {
        "volumes": [
          {
            "name": "kube-api-access-vgs8l",
            "projected": {
              "sources": [
                {
                  "serviceAccountToken": {
                    "expirationSeconds": 3607,
                    "path": "token"
                  }
                },
                {
                  "configMap": {
                    "name": "kube-root-ca.crt",
                    "items": [
                      {
                        "key": "ca.crt",
                        "path": "ca.crt"
                      }
                    ]
                  }
                },
                {
                  "downwardAPI": {
                    "items": [
                      {
                        "path": "namespace",
                        "fieldRef": {
                          "apiVersion": "v1",
                          "fieldPath": "metadata.namespace"
                        }
                      }
                    ]
                  }
                }
              ],
              "defaultMode": 420
            }
          }
        ],
        "containers": [
          {
            "name": "nginx",
            "image": "nginx:1.14.2",
            "ports": [
              {
                "containerPort": 80,
                "protocol": "TCP"
              }
            ],
            "resources": {
              "limits": {
                "memory": "512M"
              },
              "requests": {
                "memory": "512M"
              }
            },
            "volumeMounts": [
              {
                "name": "kube-api-access-vgs8l",
                "readOnly": true,
                "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount"
              }
            ],
            "terminationMessagePath": "/dev/termination-log",
            "terminationMessagePolicy": "File",
            "imagePullPolicy": "IfNotPresent"
          }
        ],
        "restartPolicy": "Always",
        "terminationGracePeriodSeconds": 30,
        "dnsPolicy": "ClusterFirst",
        "serviceAccountName": "default",
        "serviceAccount": "default",
        "nodeName": "workernode",
        "securityContext": {},
        "schedulerName": "default-scheduler",
        "tolerations": [
          {
            "key": "node.kubernetes.io/not-ready",
            "operator": "Exists",
            "effect": "NoExecute",
            "tolerationSeconds": 300
          },
          {
            "key": "node.kubernetes.io/unreachable",
            "operator": "Exists",
            "effect": "NoExecute",
            "tolerationSeconds": 300
          }
        ],
        "priority": 0,
        "enableServiceLinks": true,
        "preemptionPolicy": "PreemptLowerPriority"
      },
      "status": {
        "phase": "Running",
        "conditions": [
          {
            "type": "PodReadyToStartContainers",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2024-12-04T04:37:00Z"
          },
          {
            "type": "Initialized",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2024-12-04T04:36:59Z"
          },
          {
            "type": "Ready",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2024-12-04T04:37:00Z"
          },
          {
            "type": "ContainersReady",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2024-12-04T04:37:00Z"
          },
          {
            "type": "PodScheduled",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2024-12-04T04:36:59Z"
          }
        ],
        "hostIP": "10.0.2.15",
        "hostIPs": [
          {
            "ip": "10.0.2.15"
          }
        ],
        "podIP": "10.42.1.25",
        "podIPs": [
          {
            "ip": "10.42.1.25"
          }
        ],
        "startTime": "2024-12-04T04:36:59Z",
        "containerStatuses": [
          {
            "name": "nginx",
            "state": {
              "running": {
                "startedAt": "2024-12-04T04:37:00Z"
              }
            },
            "lastState": {},
            "ready": true,
            "restartCount": 0,
            "image": "docker.io/library/nginx:1.14.2",
            "imageID": "docker.io/library/nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d",
            "containerID": "containerd://bffce85e1a22527a3b9ea8b13c87ca28d3ae648fc2068cd2493de51d0b89039c",
            "started": true
          }
        ],
        "qosClass": "Burstable"
      }
    }
  ]
}
```
- Without proper token it returns 401 and 403 when resource out of scope gets called.

- To get started
  - Either create a user or service account. Example of service account.
  - `kubectl create serviceaccount remote-access -n kube-system --dry-run=client -o yaml > remote_access_sa.yaml`
  - `kubectl create token remote-access > remote_access_token.txt`
  - `kubectl create clusterrolebinding remote-access-binding --clusterrole=cluster-admin  --serviceaccount=kube-system:remote-access --dry-run=client -o yaml > rolebinding_remote_access_sa.yaml`
  - `kubectl apply -f *`
  - Here I'm using an existing role to bind to it. Roles can also be created using [ClusterRole Kind](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#clusterrole-example) and later bind the users/service account using [Role Binding Kind](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding).


### To access from remote machine over HTTPS
- Kubernetes API server runs on port 6443 and to connect securely, I need certificates to validate my self as a valid user.
- Using certificates, there is no need of JWT token as we will have key pair to authenticate. `.key` is private key where `.crt` is a public key. 
  - Kubernetes works over HTTPS and uses self signed certificates to validate the calls.
  - `/var/lib/rancher/k3s/server/tls/` has all certificates used by components of K3S.
  - `client-ca.crt` and `client-ca.key`: These are typically used to validate the certificates of clients trying to connect. Used for authentication when a client presents its certificate.
  - `server-ca.crt` and `server-ca.key`: These are used to sign and validate the API server's and client certificates. If you want to generate certificates for a client (e.g., for curl), you must use the server-ca files.
- **client-ca** will validate you whether you are a valid user or not. **server-ca** will validate the api call and permission against it.
- Here's an example without certs
```
curl https://192.168.56.50:6443/api/v1/namespaces/kube-system/pods

curl: (60) SSL certificate problem: self-signed certificate in certificate chain
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.

```
- Let's generate Certs

```
openssl genrsa -out python-api.key 2048 # Generating Private RSA key

openssl req -new -key python-api.key -out python-api.csr -subj "/CN=python-api" # Generating Certificate Signing Request (CSR) with Common Name (CN).

sudo openssl x509 -req -in python-api.csr -CA /var/lib/rancher/k3s/server/tls/client-ca.crt \
-CAkey /var/lib/rancher/k3s/server/tls/client-ca.key \
-CAcreateserial -out python-api.crt \
-days 365 # Using k3s own client CA (certificate authority) for signing and generating certificate.

```
- Now, when you pass these two file that is **python-api.key** and **python-api.crt**, *client-ca* will recongize you as valid user but what about the api calls?
- you need to copy server-ca.crt (public key) to remote machine and passed along with others too.

```
curl   --cert python-api.crt --key python-api.key --cacert server-ca.crt https://192.168.56.50:6443/api/v1/namespaces/kube-system/pods
```
- Well, you will get 403

- Because **python-api** user is not binded to any role. To do that

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  creationTimestamp: null
  name: python-api-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin # existing role
subjects:
- kind: User
  name: python-api
  namespace: kube-system
```
- Now, you will get the output.
