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