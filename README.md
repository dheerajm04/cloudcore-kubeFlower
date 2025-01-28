# KubeEdge Setup for CloudCore and EdgeCore

This guide provides instructions to set up KubeEdge CloudCore and EdgeCore using Docker, Kubernetes (Kind), and keadm.

---

## Prerequisites
Ensure your system has the following installed:
- Linux (Ubuntu 20.04 or later recommended)
- Docker
- Kubernetes CLI (kubectl)
- Kind
- keadm (KubeEdge tool)

---

## CloudCore Setup

1. **Update System Packages**
   ```bash
   sudo apt update
   sudo apt install docker.io -y
   sudo systemctl enable docker
   sudo systemctl status docker
   ```

2. **Install Kind**
   ```bash
   curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.11.1/kind-$(uname)-amd64"
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```

3. **Install kubectl**
   ```bash
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
   ```

4. **Create Kubernetes Cluster**
   ```bash
   sudo kind create cluster
   ```

5. **Download and Install keadm**
   ```bash
   wget https://github.com/kubeedge/kubeedge/releases/download/v1.12.1/keadm-v1.12.1-linux-amd64.tar.gz
   tar -zxvf keadm-v1.12.1-linux-amd64.tar.gz
   sudo cp keadm-v1.12.1-linux-amd64/keadm/keadm /usr/local/bin/keadm
   ```

6. **Initialize CloudCore**
   Replace `192.168.190.5` with your machine's IP address.
   ```bash
   sudo keadm deprecated init --advertise-address=192.168.190.5 --kubeedge-version=1.12.1 --kube-config=/root/.kube/config
   ```

7. **Generate Token**
   ```bash
   sudo keadm gettoken
   ```
   Save the generated token for the EdgeCore setup.

8. **Verify Node Registration**
   ```bash
   sudo kubectl get nodes
   ```

---

## EdgeCore Setup

1. **Update System Packages**
   ```bash
   sudo apt update
   sudo apt install docker.io -y
   sudo systemctl enable docker
   sudo systemctl status docker
   ```

2. **Install Kind**
   ```bash
   curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.11.1/kind-$(uname)-amd64"
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```

3. **Install kubectl**
   ```bash
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
   ```

4. **Create Kubernetes Cluster**
   ```bash
   sudo kind create cluster
   ```

5. **Download and Install keadm**
   ```bash
   wget https://github.com/kubeedge/kubeedge/releases/download/v1.12.1/keadm-v1.12.1-linux-amd64.tar.gz
   tar -zxvf keadm-v1.12.1-linux-amd64.tar.gz
   sudo cp keadm-v1.12.1-linux-amd64/keadm/keadm /usr/local/bin/keadm
   ```

6. **Disable Environment Check for EdgeCore**
   ```bash
   sudo systemctl set-environment CHECK_EDGECORE_ENVIRONMENT="false"
   ```

7. **Join EdgeCore to CloudCore**
   Replace `CloudCore-IP` with the CloudCore machine's IP address and `${token}` with the token generated earlier.
   ```bash
   sudo keadm join --cloudcore-ipport="CloudCore-IP:10000" --token=${token} --kubeedge-version=v1.12.1
   ```

8. **Verify EdgeCore Status**
   ```bash
   sudo systemctl status edgecore
   ```

---

## Notes
- Replace placeholder values like `CloudCore-IP` and `192.168.190.5` with actual IP addresses.
- Make sure ports used by CloudCore and EdgeCore are open and accessible.
- For more details, refer to the [KubeEdge Documentation](https://kubeedge.io/en/docs/).
