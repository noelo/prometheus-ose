
1. oc new-app prom/prometheus
2. oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:prometheus:default
3. oc expose service prometheus
4. oc create -f prom-configmap.yml
5. oc volume dc/prometheus --add --overwrite -t configmap --configmap-name=prometheus --name=prometheus-volume-1 -m=/etc/prometheus/config
6. oc patch dc/prometheus -p '{"spec":{"template":{"spec":{"containers":[{"name": "prometheus","args": ["-config.file=/etc/prometheus/config/prometheus.yml","-storage.local.path=/prometheus","-web.console.libraries=/etc/prometheus/console_libraries","-web.console.templates=/etc/prometheus/consoles"]}]}}}}'
7. oc create -f - <<EOF
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
8. oc volume dc/prometheus --add --overwrite -t persistentVolumeClaim --claim-name=prometheus --name=prometheus-volume -m=/prometheus

Debug notes - to test api access 

curl -v  -H "Authorization: Bearer xxxxxxxxx" https://ose-master.nocosetest.com:8443/api/v1/pods
