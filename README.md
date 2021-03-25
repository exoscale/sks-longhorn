# Exoscale Longhorn Examples

These are examples of using Longhorn on Exoscale SKS

## Installing Longhorn

Make sure to follow the quick start guide for SKS first: https://community.exoscale.com/documentation/sks/quick-start/

Simple install longhorn by applying Longhorn's manifest:  
`kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.1.0/deploy/longhorn.yaml`

Access longhorn, using port-forwarding (use http://127.0.0.1:7000 as the URL in your browser then):  
`kubectl port-forward deployment/longhorn-ui 7000:8000 -n longhorn-system`

## Examples / Configurations

##### pvcpod.yaml
This creates a simple Ubuntu-Pod with a single Longhorn volume attached.  
You bash into this pod, by typing: `kubectl exec -it example-pod -- /bin/bash`

##### statefulset.yaml
This creates a statefulset with 3 replicas/pods - this pattern is often used for databases.
Each Pod, gets its own volume assigned. So database-pod-0, always gets volume database-volume-0, even when the Pod is rescheduled or killed. See [this example](https://longhorn.io/docs/1.1.0/snapshots-and-backups/backup-and-restore/restore-statefulset/), when you want to restore backups of statefulsets.

This requries the ReadWriteOnce mode (one Volume can only be attached to one Pod). If you wish to use a shared Volume between all Pods in a set, you would use a deployment instead of a statefulset, with a ReadWriteMany volume. (This however always comes with a performance impact).

##### configuration-backup.yaml
This manifest generates a secret, you can use in longhorn for backup to Exoscale SOS (S3 storage). Make sure to generate an IAM key/secret pair, and insert them together with the zone in base64 format into the *.yaml* manifest before applying.  
Then go to `Longhorn UI -> Settings -> Backup`, insert e.g. `s3://your-bucket-name@de-fra-1/` into Backup Target and `exoscale-sos-secret` as credential secret name (name specified in the manifest under metadata).
You can automate backups using a custom storage class [see here](https://longhorn.io/docs/1.1.0/references/examples/#storageclass)