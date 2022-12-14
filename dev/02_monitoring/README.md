## Install AWS Metrics

Install metric's server
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Check installation
```bash
kubectl get deployment metrics-server -n kube-system
```

Show metrics
```bash
kubectl top nodes
kubectl top pods
```


## Install prometheus

### EKS

Be sure that node has the permission `arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy`

Install Add-on "Amazon EBS CSI Driver" on EKS cluster

### With helm

Add repository
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Install chart
```bash
helm install prometheus prometheus-community/prometheus -n monitoring
```

Delete chart
```bash
helm delete prometheus -n monitoring
```


## Install fluent bit
[https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html)

The clust must to have the `CloudWatchLogsFullAccess` permission.

Create namespace amazon-cloudwatch
```bash
kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/cloudwatch-namespace.yaml
```

Run the following command to create a ConfigMap named cluster-info with the cluster name and the Region to send logs to
```bash
kubectl create configmap fluent-bit-cluster-info \
--from-literal=cluster.name=k8s-edcilo-v2-eks \
--from-literal=http.server=On \
--from-literal=http.port=2020 \
--from-literal=read.head=Off \
--from-literal=read.tail=Off \
--from-literal=logs.region=us-east-1 \
-n amazon-cloudwatch
```

Download and deploy the Fluent Bit daemonset to the cluster by running one of the following commands.
```bash
kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/fluent-bit/fluent-bit.yaml
```

Validate the deployment by entering the following command. Each node should have one pod named fluent-bit-*.
```bash
kubectl get pods -n amazon-cloudwatch
```
