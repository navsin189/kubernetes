## Solution

### Steps
**Install Clickhouse Operator**
- `curl -s https://raw.githubusercontent.com/Altinity/clickhouse-operator/master/deploy/operator-web-installer/clickhouse-operator-install.sh | OPERATOR_NAMESPACE=test-clickhouse-operator bash`
- `kubectl get all -n test-clickhouse-operator`

