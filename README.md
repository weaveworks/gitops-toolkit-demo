# DEPRECATED: gitops-toolkit-demo
GoTK Demo

## Pre-requisites

- kubectl

```sh
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```
- helm

```sh
curl -L https://get.helm.sh/helm-v3.1.2-linux-amd64.tar.gz -o helm.tar.gz
tar xvfz helm.tar.gz linux-amd64/helm
chmod +x ./linux-amd64/helm
sudo mv ./linux-amd64/helm /usr/local/bin
rm -rf ./linux-amd64
rm helm.tar.gz
```

- fluxctl

```sh
curl -Ls https://fluxcd.io/install | sh && \
sudo mv $HOME/.fluxcd/bin/fluxctl /usr/local/bin/fluxctl
```

- direnv

```sh
curl -sfL https://direnv.net/install.sh | bash
echo "eval '$(direnv hook bash)'" >> ~/.bashrc
source ~/.bashrc
```

## Setup

- Create a `.envrc` based on `.envrc.example`
- Flux

```sh
kubectl create namespace fluxcd
helm repo add fluxcd https://charts.fluxcd.io
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml
helm upgrade -i flux fluxcd/flux --wait \
    --namespace fluxcd \
    --set git.url=git@github.com:${GIT_USER}/${GIT_REPO_NAME}.git \
    --set git.path="flux-mgmt" \
    --set git.timeout=120s \
    --set git.pollInterval=1m \
    --set syncGarbageCollection.enabled=true \
    --set rbac.create=true
fluxctl identity --k8s-fwd-ns fluxcd
```

- Helm Operator

```sh
helm upgrade helm-operator fluxcd/helm-operator \
    --force \
    -i \
    --wait \
    --namespace fluxcd \
    --set helm.versions=v3
```
