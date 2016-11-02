
# Prometheus on OCP #


```
oc new-project prometheus --display-name='Prometheus' --description='Prometheus'
oc new-app prom/prometheus
oc adm policy add-cluster-role-to-user cluster-reader system:serviceaccount:prometheus:default
oc expose service prometheus
oc create -f prom-configmap.yml
oc volume dc/prometheus --add --overwrite -t configmap --configmap-name=prometheus --name=prometheus-volume-1 -m=/etc/prometheus/config
oc patch dc/prometheus -p '{"spec":{"template":{"spec":{"containers":[{"name": "prometheus","args": ["-config.file=/etc/prometheus/config/prometheus.yml","-storage.local.path=/prometheus","-web.console.libraries=/etc/prometheus/console_libraries","-web.console.templates=/etc/prometheus/consoles"]}]}}}}'
oc create -f - <<EOF
{
  "apiVersion": "v1",
  "kind": "PersistentVolumeClaim",
  "metadata": {
    "name": "prometheus"
  },
  "spec": {
    "accessModes": [ "ReadWriteOnce" ],
    "resources": {
     "requests": {
        "storage": "1Gi"
      }
    }
  }
}
EOF
oc volume dc/prometheus --add --overwrite -t persistentVolumeClaim --claim-name=prometheus --name=prometheus-volume -m=/prometheus
```

Debug notes - to test api access 

curl -v  -H "Authorization: Bearer xxxxxxxxx" https://ose-master.nocosetest.com:8443/api/v1/pods
