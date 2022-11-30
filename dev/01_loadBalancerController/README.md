## Install AWS Load Balancer Controller

### Attach OpenID

Create Open Id and associate to cluster
```bash
eksctl utils associate-iam-oidc-provider \
    --region "us-east-1" \
    --cluster "k8s-edcilo-v2-eks" \
    --approve
```
Go to the next link and check the new open id
[https://us-east-1.console.aws.amazon.com/iamv2/home#/identity_providers](https://us-east-1.console.aws.amazon.com/iamv2/home#/identity_providers)

### IAM Policy

Download IAM policy
```bash
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.3/docs/install/iam_policy.json
```

Create a new policy
```bash
aws iam create-policy \
    --policy-name k8s-edcilo-v2-AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

### IAM Role

Create a new account
```bash
eksctl create iamserviceaccount \
  --region=us-east-1 \
  --cluster=k8s-edcilo-v2-eks \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name "k8s-edcilo-v2-AmazonEKSLoadBalancerControllerRole" \
  --attach-policy-arn=arn:aws:iam::790407312184:policy/k8s-edcilo-v2-AWSLoadBalancerControllerIAMPolicy \
  --approve
```

Validate that the account has been created
```bash
k8s describe ServiceAccount aws-load-balancer-controller -n kube-system
```

### Helm

Add repository
```bash 
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

Install chart
```bash 
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=k8s-edcilo-v2-eks \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

Delete chart
```bash
helm delete aws-load-balancer-controller -n kube-system
```
