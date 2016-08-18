
1. oc new-app prom/prometheus
2. oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:prometheus:default
3. oc expose service prometheus
4. oc create -f prom-configmap.yml
5. Edit the DC to

[1] Add the config map volumes
```
volumeMounts:
- mountPath: /etc/prometheus/config
  name: config-volume

	volumes:
    - configMap:
        name: prometheus
      name: config-volume
```


[2] Change the app arguments to pick up the new config location
```
spec:
  containers:
  - args:
    - -config.file=/etc/prometheus/config/prometheus.yml
    - -storage.local.path=/prometheus
    - -web.console.libraries=/etc/prometheus/console_libraries
    - -web.console.templates=/etc/prometheus/consoles
```

Debug notes - to test api access 

curl -v  -H "Authorization: Bearer xxxxxxxxx" https://ose-master.nocosetest.com:8443/api/v1/pods
