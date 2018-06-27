Longhorn Manager [![Build Status](https://drone8.rancher.io/api/badges/rancher/longhorn-manager/status.svg)](https://drone8.rancher.io/rancher/longhorn-manager)
========

Manager for Longhorn.

## Requirement

1. Existing Kubernetes Cluster 1.8+
2. Make sure `iscsiadm`/`open-iscsi` has been installed on the host.
3. Make sure `jq`, `findmnt`, `curl` has been installed on the host, for the Longhorn Flexvolume Driver.

## Build

`make`

## Deployment

`kubectl create -Rf deploy`

It will deploy the following components in the `longhorn-system` namespace:
1. Longhorn Manager
2. Longhorn Flexvolume Driver for Kubernetes
3. Longhorn UI

## Cleanup

Longhorn CRD has finalizers in them, so user should delete the volumes and related resource first, give manager a chance to clean up after them.
### 1. Clean up volume and related resources
```
kubectl -n longhorn-system delete lhv --all
```
Check the result using:
```
kubectl -n longhorn-system get lhv
kubectl -n longhorn-system get lhe
kubectl -n longhorn-system get lhr
```
Make sure all reports `No resources found.` before continuing.

### 2. Clean up engine images
```
kubectl -n longhorn-system delete lhei --all
```
### 3. Clean up nodes
```
kubectl -n longhorn-system delete lhn --all
```
### 4. Clean up the manager and related pods.
```
kubectl delete -Rf deploy
```

## Integration test

See [longhorn-tests repo](https://github.com/rancher/longhorn-tests/tree/master/manager/integration)

## Notes:

### Google Kubernetes Engine
You will need to create cluster-admin role binding for yourselves before creating the deployment, see
[here](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control) for details.
```
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=<name@example.com>
```

### Flexvolume Plugin Directory
By default we're using the [default Flexvolume Plugin directory](https://github.com/kubernetes/community/blob/master/contributors/devel/flexvolume.md#prerequisites), which is `/usr/libexec/kubernetes/kubelet-plugins/volume/exec/`.

For GKE 1.8+, it should be at: `/home/kubernetes/flexvolume`.

You may need to change `deploy/deploy.yaml` volume `flexvolume-longhorn-mount` location according to your own environment.

## License
Copyright (c) 2014-2018 [Rancher Labs, Inc.](http://rancher.com)

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
