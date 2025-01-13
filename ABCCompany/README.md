# AWSCLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure
```

## KUBECTL

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

## EKSCTL

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

## Create EKS CLUSTER

```bash
eksctl create cluster --name=my-eks \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --version=1.30 \
                      --without-nodegroup

eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster my-eks \
    --approve

eksctl create nodegroup --cluster=my-eks \
                       --region=us-east-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=3 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=viranz \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
```
* Open INBOUND TRAFFIC IN ADDITIONAL Security Group
* Create Servcie account/ROLE/BIND-ROLE/Token

## Create Service Account, Role & Assign that role, And create a secret for Service Account and geenrate a Token

### Creating Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```

### Create Role


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - secrets
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

### Bind the role to service account


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins 
```
### Create Cluster role & bind to Service Account
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: jenkins-cluster-role
rules:
- apiGroups: [""]
  resources: ["persistentvolumes"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jenkins-cluster-role-binding
subjects:
- kind: ServiceAccount
  name: jenkins
  namespace: webapps
roleRef:
  kind: ClusterRole
  name: jenkins-cluster-role
  apiGroup: rbac.authorization.k8s.io

```
### Generate token using service account in the namespace

[Create Token](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#:~:text=To%20create%20a%20non%2Dexpiring,with%20that%20generated%20token%20data.)

### Alert Rules Configuration


```yaml
groups:
  - name: alert_rules # Name of the alert rules group
rules:
  - alert: InstanceDown
expr: up == 0 # Expression to detect instance down
for: 1m
labels:
severity: "critical"
annotations:
summary: "Endpoint {{ $labels.instance }} down"
description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 1 minute."
  - alert: WebsiteDown
expr: probe_success == 0 # Expression to detect website down
for: 1m
labels:
severity: critical
annotations:
description: The website at {{ $labels.instance }} is down.
summary: Website down
  - alert: HostOutOfMemory
expr: node_memory_MemAvailable / node_memory_MemTotal * 100 < 25 # Expression to detect low memory
for: 5m
labels:
severity: warning
annotations:
summary: "Host out of memory (instance {{ $labels.instance }})"
description: "Node memory is filling up (< 25% left)\n VALUE = {{ $value }}\n LABELS: {{ $labels }}"
  - alert: HostOutOfDiskSpace
expr: (node_filesystem_avail{mountpoint="/"} * 100) / node_filesystem_size{mountpoint="/"} < 50 # Expression to detect low disk space
for: 1s
labels:
severity: warning
annotations:
summary: "Host out of disk space (instance {{ $labels.instance }})"
description: "Disk is almost full (< 50% left)\n VALUE = {{ $value }}\n LABELS: {{ $labels }}"
  - alert: HostHighCpuLoad
expr: (sum by (instance) (irate(node_cpu{job="node_exporter_metrics",mode="idle"}[5m]))) > 80 # Expression to detect high CPU load
for: 5m
labels:
severity: warning
annotations:
summary: "Host high CPU load (instance {{ $labels.instance }})"
description: "CPU load is > 80%\n VALUE = {{ $value }}\n LABELS: {{ $labels }}"
  - alert: ServiceUnavailable
expr: up{job="node_exporter"} == 0 # Expression to detect service unavailability
for: 2m
labels:
severity: critical
annotations:
summary: "Service Unavailable (instance {{ $labels.instance }})"
description: "The service {{ $labels.job }} is not available\n VALUE = {{ $value }}\n LABELS: {{ $labels }}"
  - alert: HighMemoryUsage
expr: (node_memory_Active / node_memory_MemTotal) * 100 > 90 # Expression to detect high memory usage
for: 10m
labels:
severity: critical
annotations:
summary: "High Memory Usage (instance {{ $labels.instance }})"
description: "Memory usage is > 90%\n VALUE = {{ $value }}\n LABELS: {{ $labels }}"
  - alert: FileSystemFull
expr: (node_filesystem_avail / node_filesystem_size) * 100 < 10 # Expression to detect file system almost full
for: 5m
labels:
severity: critical
annotations:
summary: "File System Almost Full (instance {{ $labels.instance }})"
description: "File system has < 10% free space\n VALUE = {{ $value }}\n LABELS: {{ $labels }}"
```

### Routing Configuration


```yaml
route:
group_by: ['alertname'] # Group by alert name
group_wait: 30s # Wait time before sending the first notification
group_interval: 5m # Interval between notifications
repeat_interval: 1h # Interval to resend notifications
receiver: 'email-notifications' # Default receiver
receivers:
  - name: 'email-notifications' # Receiver name
email_configs:
  - to: oviran@gmail.com # Email recipient
from: test@gmail.com # Email sender
smarthost: smtp.gmail.com:587 # SMTP server
auth_username: your_email # SMTP auth username
auth_identity: your_email # SMTP auth identity
auth_password: "bdmq omqh vvkk zoqx" # SMTP auth password
send_resolved: true # Send notifications for resolved alerts
```

### Inhibition Rules


```yaml
inhibit_rules:
  - source_match:
severity: 'critical' # Source alert severity
target_match:
severity: 'warning' # Target alert severity
equal: ['alertname', 'dev', 'instance'] # Fields to match
```
