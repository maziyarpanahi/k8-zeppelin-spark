# ZEPPELIN

## APPLY

kubectl apply -f zeppelin-pv-nfs.yaml
kubectl get pv --all-namespaces
kubectl apply -f zeppelin-pvc-nfs.yaml
kubectl get pvc --all-namespaces
kubectl apply -f zeppelin-server.yaml
kubectl get pod --all-namespaces

## DELETE

kubectl delete -f zeppelin-server.yaml
kubectl delete -f zeppelin-pv-nfs.yaml
kubectl delete -f zeppelin-pvc-nfs.yaml

## Volumes

### Executors

```sh
%spark.conf

spark.executor.instances  3
spark.driver.extraJavaOptions "-Divy.cache.dir=/tmp -Divy.home=/tmp"

spark.jsl.settings.pretrained.cache_folder /spark/work-dir

# this doesn't work in Zeppelin since the Driver has its template inside the 100-interpreter-spec.yaml
spark.kubernetes.driver.volumes.persistentVolumeClaim.pv-nfs-pv4.mount.path /spark/work-dir
spark.kubernetes.driver.volumes.persistentVolumeClaim.pv-nfs-pv4.mount.readOnly false
spark.kubernetes.driver.volumes.persistentVolumeClaim.pv-nfs-pv4.options.sizeLimit 1Gi
spark.kubernetes.driver.volumes.persistentVolumeClaim.pv-nfs-pv4.options.claimName pvc-nfs-pv4

spark.kubernetes.executor.volumes.persistentVolumeClaim.pv-nfs-pv4.mount.path /spark/work-dir
spark.kubernetes.executor.volumes.persistentVolumeClaim.pv-nfs-pv4.mount.readOnly false
spark.kubernetes.executor.volumes.persistentVolumeClaim.pv-nfs-pv4.options.sizeLimit 1Gi
spark.kubernetes.executor.volumes.persistentVolumeClaim.pv-nfs-pv4.options.claimName pvc-nfs-pv4
```

### Driver

Add the volume and volumeMount inside 100-interpreter-spec.yaml for the Driver to share the NFS path with the executors
