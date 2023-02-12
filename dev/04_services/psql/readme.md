## Install with Helm

add repository 
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

postgres configuration is in the file **helm_postgres_config.yml**.

install chart
```bash
helm install raspi-postgres-dev -f helm_postgres_config.yml bitnami/postgresql -n dev
```

uninstall chart 
```bash 
helm uninstall raspi-postgres-dev -n dev
```
