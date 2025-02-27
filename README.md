# k3ssetup-pratham
steps for K3S setup 
# **K3s Kubernetes Cluster Setup on Raspberry Pi 5**

## **Prerequisites**
Before starting the K3s installation, ensure you have the following:
- A **Raspberry Pi 5** with a fresh Ubuntu installation.
- **Root or sudo access**.
- **Stable internet connection**.
- **At least 2GB RAM and sufficient storage space** (8GB+ recommended).

---

## **Step 1: Install Required Dependencies**
To ensure K3s runs smoothly, install the required dependencies:

```sh
sudo apt update 
sudo apt install -y curl iptables conntrack socat
```

This installs essential networking and communication tools for K3s.

---

## **Step 2: Install K3s on the Master Node**
Run the following command to install **K3s**:

```sh
curl -sfL https://get.k3s.io | sh -
```

Verify that K3s is running:

```sh
sudo systemctl status k3s
```

Check if the cluster is up and running:

```sh
kubectl get nodes
```

---

## **Step 3: Retrieve Node Token**
The **Node Token** is required to add worker nodes. Run the following command to retrieve it:

```sh
sudo cat /var/lib/rancher/k3s/server/node-token
```

Save this token securely if you plan to add worker nodes.

---

## **Step 4: Enable K3s for Automatic Startup**
Ensure that K3s starts automatically after a reboot:

```sh
sudo systemctl enable k3s
```

For worker nodes, run:

```sh
sudo systemctl enable k3s-agent
```

---

## **Step 5: Install and Configure Flannel CNI**
By default, K3s comes with Flannel networking, but if needed, you can manually apply it with the correct PodCIDR.

### **Download the Flannel YAML Manifest**
```sh
curl -O https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

### **Modify the Flannel Configuration**
Edit the Flannel configuration file:

```sh
vim kube-flannel.yml
```

Locate the `net-conf.json` section and modify it as follows:

```yaml
net-conf.json: |
  {
    "Network": "10.42.0.0/16",
    "Backend": {
      "Type": "vxlan"
    }
  }
```

Save and exit the file (`ESC` → `:wq` → `Enter`).

### **Apply the Updated Flannel Configuration**
```sh
kubectl apply -f kube-flannel.yml
```

---

## **Step 6: Verify Deployment**
To check if all Kubernetes components are running correctly, use:

```sh
kubectl get pods -A
```

You should see running pods, including `coredns`, `traefik`, `metrics-server`, and `flannel`.

---

## **Final Validation**
Run these commands to ensure everything is working correctly:

- Check nodes:
  ```sh
  kubectl get nodes -o wide
  ```
- Check services:
  ```sh
  kubectl get svc -A
  ```
- Test pod-to-pod communication:
  ```sh
  kubectl run test --image=busybox --restart=Never --rm -it -- nslookup kubernetes.default
  ```

---

## **Conclusion**
You have successfully set up a **K3s Kubernetes cluster** on a **Raspberry Pi 5** with Flannel networking. 🚀 This setup is lightweight and optimized for IoT and edge computing environments.

For further troubleshooting, check logs:
```sh
sudo journalctl -u k3s -f
```

Enjoy your Kubernetes journey! 🎯


