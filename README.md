# Kubernetes Cluster Setup on AWS EC2 using kubeadm

This guide will walk you through creating a Kubernetes cluster with kubeadm using two AWS EC2 instances: a Master Node and a Worker Node.

## Step 1: Create EC2 Instances

1. **Launch Instances:**
    - **Amazon Machine Image (AMI):** Ubuntu
    - **Instance Type:** t2.medium
    - **Security Group:** Edit inbound rules to allow all traffic with All IPv4
    - **Number of Instances:** 2
    - **Launch Instances**

2. **Naming:**
    - Name one instance as `Master Node` and the other as `Worker Node`.

3. **Connect:**
    - Use Git Bash or PuTTY to connect to each instance separately.
  

**Execute the following commands from step 2 to step 13 on both (worker and master nodes) instances:**


## Step 2: Update the System

```bash
sudo apt update -y
```

## Step 3: Install Docker

Install Docker to enable containerization:

```bash
sudo apt install docker.io -y
```

## Step 4: Update the Package List Again

Ensure the package list is up-to-date:

```bash
sudo apt-get update
```

## Step 5: Install Prerequisites

Install necessary tools and certificates:

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl
```

## Step 6: Create Directory for Kubernetes Keyrings

Create the directory for Kubernetes keyrings with appropriate permissions:

```bash
sudo mkdir -p -m 755 /etc/apt/keyrings
```

## Step 7: Download and Add the Kubernetes GPG Key

Download the Kubernetes GPG key and add it to the keyrings:

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

## Step 8: Set Permissions for the Keyring File

Set the correct permissions for the keyring file:

```bash
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

## Step 9: Add the Kubernetes Repository

Add the Kubernetes APT repository to your system:

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

## Step 10: Set Permissions for the Repository List File

Set the correct permissions for the repository list file:

```bash
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
```

## Step 11: Update the Package List Again

Ensure the package list includes the Kubernetes repository:

```bash
sudo apt-get update
```

## Step 12: Install Kubernetes Components

Install `kubelet`, `kubeadm`, and `kubectl`:

```bash
sudo apt-get install -y kubelet kubeadm kubectl
```

## Step 13: Prevent Automatic Updates of Kubernetes Components

Hold the Kubernetes components at their current version to prevent automatic updates:

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

## Steps for the Master Node Only

Execute the following commands as the root user on the Master Node.

### Step 14: Become the Root User

Switch to the root user:

```bash
sudo su -
```
### Step 15: Initiate Kubeadm

Initiate `kubeadm`:

```bash
kubeadm init
```
**Note:** Copy the commands from the output of `kubeadm init` and save them for later use.

![Kubernetes-op](https://github.com/user-attachments/assets/b163ef3e-ebd9-4cd8-acf3-b28754e9a374)

### Step 16: Set Up the Kubernetes Configuration

Execute the following commands from the `kubeadm init` output:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
export KUBECONFIG=/etc/kubernetes/admin.conf
```
### Step 17: Check for Nodes

Check the status of the nodes:

```bash
kubectl get nodes
```
The master node will be listed but not yet ready.

### Step 18: Install Weave Net for Network Management

Apply the Weave Net DaemonSet for Kubernetes networking:

```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

Check the node status again:

```bash
kubectl get nodes
```
The master node should now be ready.

## Step 19: Join the Worker Node to the Master Node

Use the command from the `kubeadm init` output to join the worker node to the master node. Execute this command on the Worker Node as the root user:

```bash
sudo su -
```

Example command:

```bash
kubeadm join 172.31.35.7:6443 --token xpde3j.7gbygg43kga40a2y --discovery-token-ca-cert-hash sha256:03395fdadf51861ed6df8d95d0dcc48b27a220089f6f927fbac3267d0d7438b7
```

Here is a screenshot of expected output

![kube-op2](https://github.com/user-attachments/assets/27d228c2-eba5-4c79-9bdf-c1526bf81b40)


### Verify the Cluster

On the Master Node, verify that the Worker Node has joined:

```bash
kubectl get nodes
```
![step19-2](https://github.com/user-attachments/assets/2e69f3d9-aa94-4737-9612-8fb00b456af6)

You should see both the Master Node and Worker Node listed as ready.

## Conclusion

You now have a functional Kubernetes cluster with one Master Node and one Worker Node. You can deploy and manage your applications using this cluster.

**Note:** Remember to terminate the EC2 instances after your practice session to avoid incurring charges.

Best of luck!

