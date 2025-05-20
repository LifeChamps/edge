# FluxCD Bootstrap on Raspberry Pis

This guide explains how to install and bootstrap FluxCD on a Raspberry Pi running [MicroK8s](https://microk8s.io/). It assumes that the device has already been prepared using the instructions in the `bootstrap` directory.

## Prerequisites

1. **MicroK8s installed** on the Raspberry Pi and running correctly.
2. **kubectl configured** to communicate with the local cluster:
   ```shell
   microk8s status --wait-ready
   alias kubectl='microk8s.kubectl'
   ```
3. **Git installed** on the Pi and a personal access token (PAT) with permission to clone this repository.
4. The `flux` command line tool available. Install it with:
   ```shell
   curl -s https://fluxcd.io/install.sh | sudo bash
   ```

## Bootstrapping FluxCD

1. **Create a namespace for Flux:**
   ```shell
   kubectl create namespace flux-system
   ```
2. **Export your Git credentials:**
   ```shell
   export GITHUB_USER=<your-username>
   export GITHUB_TOKEN=<your-personal-access-token>
   ```
3. **Bootstrap Flux:**
   ```shell
   flux bootstrap github \
     --owner=$GITHUB_USER \
     --repository=edge \
     --branch=main \
     --path=k8s-gitops \
     --personal
   ```
   This command installs FluxCD components in the `flux-system` namespace and configures the cluster to sync with the `k8s-gitops` directory of this repository.

4. **Verify the installation:**
   ```shell
   flux get kustomizations
   flux get sources git
   ```
   All resources should report a ready status.

## Updating the Repository

After the bootstrap, any change committed to the `k8s-gitops` directory will be automatically applied to the cluster. Make sure to use pull requests when modifying Kubernetes manifests.

## Troubleshooting

If synchronization fails, check the Flux logs:
```shell
kubectl logs -n flux-system deploy/flux -f
```
You can reconcile manually using:
```shell
flux reconcile kustomization flux-system
```

For more details, refer to the [FluxCD documentation](https://fluxcd.io/docs/).
