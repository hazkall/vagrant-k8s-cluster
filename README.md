# How use this automation

Run virtualmachines

```bash

vagrant up

```

Check virtualmachines

```bash

vagrant status

```

Access kubernetes controlplane

```bash

vagrant ssh k8s-controlplane

kubectl get nodes

kubectl get pods -A

```

# Kubernetes cluster info

- Container Runtime -> CRI-O 1.25
- Pod Network -> Cilium
- Add Kubelet certificate signing protection
- Add kubectl autocompletion on user vagrant
- Remove kube-proxy and use Cilium
