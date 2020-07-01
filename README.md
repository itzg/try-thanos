This repository contains a set of Kubernetes manifest files to try out a "single node" deployment of [Thanos](https://thanos.io/) using [MinIO](https://min.io/) as an S3 object store.

## Quick start

Create Minio secret, changing at least the `secret-key`, such as generated with `openssl rand -hex 10`

```shell script
kubectl create secret generic minio \
    --from-literal=access-key=minioadmin \
    --from-literal=secret-key=minioadmin
```

> If you're in a hurry and using a local cluster such as Docker Desktop, just use that secret as shown.

Copy `configs/thanos-objstore-config.yaml` to `/tmp` and modify `secret_key` and `access_key` according to the secret, above.

Create a secret from that modified file:
```shell script
kubectl create secret generic thanos-objstore-config \
    --from-file=thanos-objstore-config.yaml=/tmp/thanos-objstore-config.yaml
```

> If you were in a hurry, just use `--from-file=thanos-objstore-config.yaml=configs/thanos-objstore-config.yaml`

> Some of the service manifests use `type: LoadBalancer`. On Docker Desktop those will bind to the respective desktop port. With other cluster types you may want to change those accordingly, such as `type: NodePort`.

Start up just Minio using:

```shell script
kubectl apply -f minio-deployment.yaml -f minio-ext-service.yaml
```

Connect to Minio using the access key and secret, above, and create a bucket named "thanos". If deploying on Docker Desktop, Minio is available at <http://localhost:9000/minio>.

Now apply the remaining manifests in this directory using the following. 

```shell script
kubectl apply -f .
```

Some URLs of interest are as follows, assuming a Docker Desktop deployment:
- Grafana @ <http://localhost:3000>
- Minio @ <http://localhost:9000/minio>
- Thanos remote-write endpoint compatible with Prometheus @ <http://localhost:19291/api/v1/receive>