# kind basics

source: https://www.youtube.com/playlist?list=PL1xPpLHBFNWvKPI0ofmFzJn9lrWiqGukT

### Setup
- Prerequisites
    - docker
        - install `sudo apt install docker.io -y`
        - verify `docker version`
            - if getting permission denied, then add the current user in docker-group `sudo usermod -aG docker ${USER}`
    - go (1.16+)
        - install `sudo apt install docker.io -y`
        - verify `go version`
    - podman
- Install kind
    - `go install sigs.k8s.io/kind@v0.29.0`
        - adjust version accordingly
        - verify `kind version`
- Use
    - `kind create cluster` - this will make a docker container named (most probably) `kind-control-plane`
        - verify `kind get clusters`
    - `docker exec -it kind-control-plane bash`
        - `kubectl get no`
        - `kubectl version`
- Create alias
    - `echo 'alias k=kubectl' >> ~/.bashrc`
- Enable autocomplete
    - `apt install -y bash-completion`
    - `echo 'source <(kubectl completion bash)' >> ~/.bashrc`
    - `echo 'source /usr/share/bash-completion/bash_completion' >> ~/.bashrc`
    - `echo 'complete -o default -F __start_kubectl k' >> ~/.bashrc`
- Make sure your cluster has `flannel`, which is a popular CNI (Container Network Interface) plugin used in k8s to provide network connectivity between pods across different nodes in a cluster.
    - `nano no-cni-config.yaml`
        ```yaml
        kind: Cluster
        apiVersion: kind.x-k8s.io/v1alpha4
        networking:
            disableDefaultCNI: true
        ```
    - `kind create cluster --config no-cni-config.yaml --name flannel`
    - `docker exec -it flannel-control-plane bash`
        - `apt update && apt install wget`
        - `wget https://github.com/containernetworking/plugins/releases/download/v1.1.1/cni-plugins-linux-amd64-v1.1.1.tgz`
        - `tar -xvf cni-plugins-linux-amd64-v1.1.1.tgz`
        - `mv bridge /opt/cni/bin`
        - At this moment the `STATUS` is set to `NotReady` - verify running in the shell `kubectl get no`
        - `kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml`
        - After about 30" to 1' the `STATUS` should change to `Ready`.
        - `kubectl get po -A`