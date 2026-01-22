# 🔥 PROJECT PHOENIX – MISSION L1: THE BROKEN LINK
## **Kubernetes Connectivity & Syntax Errors**

---

## 📡 MISSION BRIEFING

**Commander**, the Phoenix system deployment has **catastrophically failed**. Intelligence reports indicate **10 critical errors** spanning resource allocation, configuration mismatches, networking failures, and syntax mistakes. The system is **offline** and unreachable.

Your first mission as Phoenix Commander is to **debug a completely broken Kubernetes deployment** using only `kubectl` commands and your troubleshooting skills. This is a **full-spectrum diagnostic challenge** covering the most common production failures seen in real-world Kubernetes environments.

⚠️ **Warning:** This is a **high-difficulty scenario**. Every layer of the stack has failures — from resource requests to service routing.

---

## 🎯 MISSION OBJECTIVE

Deploy the **Phoenix Operations Dashboard** to the `phoenix-system` namespace with:
- ✅ Pod status: **1/1 READY and Running**
- ✅ Service exposed via **LoadBalancer** (or NodePort)
- ✅ Application accessible at `http://<EXTERNAL-IP>/`
- ✅ Health endpoint responding at `/health`
- ✅ Dynamic pod diagnostics displaying correctly

**Victory Page:** A live dashboard showing system status, pod name, node name, pod IP, and encrypted intelligence data.

---

## 📂 MISSION FILES

| File | Description | Difficulty |
|------|-------------|------------|
| **01-phoenix-challenge.yaml** | 🌑 **Dark Mode** – 10 errors, zero hints. Pure troubleshooting combat. | ⚡⚡⚡ |
| **02-phoenix-guided.yaml** | 🔦 **Intel Mode** – Every error documented with inline comments explaining the "Why". | ⚡⚡ |
| **03-phoenix-solution.yaml** | ✅ **Reference Solution** – The fully operational configuration. | Reference Only |

---

## 🛠️ TROUBLESHOOTING INTEL

### **Core Commands for This Mission**

| Command | Purpose |
|---------|---------|
| `kubectl -n phoenix-system get pods` | Check pod status (Pending, ImagePullBackOff, CrashLoopBackOff, Running) |
| `kubectl -n phoenix-system describe pod <pod-name>` | Deep dive into events, errors, and misconfigurations |
| `kubectl -n phoenix-system get svc` | Verify service configuration and external IP assignment |
| `kubectl -n phoenix-system logs <pod-name>` | View application logs for runtime errors |

### **Advanced Recon (Optional)**

```bash
# Watch resources in real-time
kubectl -n phoenix-system get pods,svc -w

# Check all events in namespace
kubectl -n phoenix-system get events --sort-by='.lastTimestamp'

# Verify ConfigMaps
kubectl -n phoenix-system get configmap
kubectl -n phoenix-system describe configmap phoenix-config

# Test service connectivity
curl http://<EXTERNAL-IP>/
curl http://<EXTERNAL-IP>/health
```

---

## 🚨 ERRORS COVERED IN THIS MISSION

This lab contains **10 intentional errors** across multiple Kubernetes components:

### **1️⃣ Resource Request CPU Overload**
- **Symptom:** Pod stuck in `Pending` state indefinitely
- **Root Cause:** Requesting `100` CPUs (should be `100m` millicores)
- **Learning Goal:** Understand Kubernetes resource units and cluster capacity limits

### **2️⃣ Container Image Name Typo**
- **Symptom:** Pod shows `ImagePullBackOff` or `ErrImagePull`
- **Root Cause:** Image name `python:3.11-slimZZZZZZ` (should be `python:3.11-slim`)
- **Learning Goal:** Image pull failures and image registry validation

### **3️⃣ ConfigMap Key Mismatch (Error 1)**
- **Symptom:** Environment variable `COMPANY_NAME` is empty or causes `CreateContainerConfigError`
- **Root Cause:** Referencing key `CORP_NAME` instead of `COMPANY_NAME`
- **Learning Goal:** ConfigMap key references in environment variables

### **4️⃣ ConfigMap Key Mismatch (Error 2)**
- **Symptom:** Environment variable `STATUS_MSG` is empty
- **Root Cause:** Referencing key `MSG_STATUS` instead of `STATUS_MSG`
- **Learning Goal:** ConfigMap troubleshooting patterns

### **5️⃣ Readiness Probe Path Error**
- **Symptom:** Pod stays `0/1 READY` despite running
- **Root Cause:** Probe checking `/healthz` instead of `/health`
- **Learning Goal:** Readiness probe failure impact on service routing

### **6️⃣ Readiness Probe Port Mismatch**
- **Symptom:** Readiness probe fails continuously
- **Root Cause:** Checking port `9000` instead of `8000`
- **Learning Goal:** Port configuration consistency

### **7️⃣ Volume Mount Path Mismatch**
- **Symptom:** Application displays "Intelligence File Not Found" message
- **Root Cause:** ConfigMap mounted as `news.txt` but app looks for `announcement.txt`
- **Learning Goal:** Volume mount path specification

### **8️⃣ Service Selector Mismatch**
- **Symptom:** Service has no endpoints, traffic doesn't reach pod
- **Root Cause:** Service selects `phoenix-web-ui` but pod is labeled `phoenix-app`
- **Learning Goal:** Label selector debugging with `kubectl get endpoints`

### **9️⃣ Service Port Mismatch**
- **Symptom:** Service accessible but connection refused/timeout
- **Root Cause:** Service `targetPort: 80` but container listens on `8000`
- **Learning Goal:** Service port forwarding configuration

### **🔟 Combined Impact**
- **Learning Goal:** Understanding how multiple failures compound and which to fix first

---

## ✅ SUCCESS CRITERIA

Your mission is **COMPLETE** when you achieve:

| Checkpoint | Validation Command | Expected Output |
|------------|-------------------|-----------------|
| **Pod Running** | `kubectl -n phoenix-system get pods` | `phoenix-deployment-xxxxx   1/1   Running` |
| **Pod Ready** | `kubectl -n phoenix-system get pods` | Pod shows `1/1` (not `0/1`) |
| **Service Endpoints** | `kubectl -n phoenix-system get endpoints` | Endpoint shows pod IP:8000 |
| **External Access** | `curl http://<EXTERNAL-IP>/` | HTML dashboard with "PHOENIX OPERATIONS" |
| **Health Check** | `curl http://<EXTERNAL-IP>/health` | `{"status":"ok"}` |
| **Dynamic Data** | Browser view | Pod name, node name, IP displayed correctly |
| **Intel Message** | Browser view | Shows "Azure Region: East US Active" |

---

## 🎓 LEARNING OUTCOMES

By completing this mission, you will master:

- ✅ **Resource unit notation** (m for millicores, Mi/Gi for memory)
- ✅ **Image pull troubleshooting** and registry validation
- ✅ **ConfigMap key reference debugging** in environment variables
- ✅ **Health probe configuration** (path and port accuracy)
- ✅ **Volume mount path specifications** for ConfigMaps
- ✅ **Service selector debugging** using labels and endpoints
- ✅ **Service port mapping** (port vs targetPort vs containerPort)
- ✅ **Multi-layer troubleshooting methodology** (bottom-up: pod → service → network)
- ✅ **Event-driven debugging** using `kubectl describe` and `kubectl get events`

---

## 🚀 DEPLOYMENT INSTRUCTIONS

### **Mode 1: Challenge (Recommended for Learning)**
```bash
# Deploy the broken configuration
kubectl apply -f 01-phoenix-challenge.yaml

# Begin systematic troubleshooting
kubectl -n phoenix-system get pods
kubectl -n phoenix-system describe pod <pod-name>

# Fix errors iteratively (edit the YAML locally)
kubectl apply -f 01-phoenix-challenge.yaml

# Validate after each fix
kubectl -n phoenix-system get pods,svc,endpoints
```

### **Mode 2: Guided (With Hints)**
```bash
# Deploy with inline error explanations
kubectl apply -f 02-phoenix-guided.yaml

# Read the comments in the YAML for troubleshooting hints
```

### **Mode 3: Solution (Reference)**
```bash
# Deploy the working configuration
kubectl apply -f 03-phoenix-solution.yaml

# Verify success
kubectl -n phoenix-system get all
curl http://$(kubectl -n phoenix-system get svc phoenix-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
```

---

## 🧹 CLEANUP

```bash
# Remove all resources when done
kubectl delete namespace phoenix-system
```

---

## 💡 COMMANDER'S TIPS

1. **Fix in order:** Start with pod errors (Pending → ImagePull → CreateContainer → Running → Ready)
2. **Always check events:** `kubectl describe pod` shows the failure timeline
3. **Validate ConfigMaps:** Use `kubectl describe configmap` to verify keys
4. **Service debugging:** Check endpoints (`kubectl get endpoints`) before troubleshooting external access
5. **Port triple-check:** containerPort (pod) → targetPort (service) → port (service external)
6. **Resource requests:** Remember `100m` = 100 millicores, `100` = 100 cores (huge difference!)
7. **Label matching:** Service selector labels must EXACTLY match pod labels
8. **Readiness blocks traffic:** Even if pod is Running, 0/1 Ready means service won't route to it

---

## 📚 REFERENCE DOCUMENTATION

- [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Services Networking](https://kubernetes.io/docs/concepts/services-networking/service/)
- [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [Resource Management](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Configure Liveness, Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

---

## 🎯 TROUBLESHOOTING WORKFLOW

```
1. kubectl get pods          → Identify pod state (Pending/ImagePull/CrashLoop/Running)
2. kubectl describe pod      → Read Events section for root cause
3. Fix YAML                  → Address the specific error
4. kubectl apply -f          → Redeploy
5. kubectl get pods          → Verify state improved
6. Repeat until 1/1 Running
7. kubectl get svc           → Check service external IP
8. kubectl get endpoints     → Verify pod is registered
9. curl http://<IP>          → Test connectivity
```

---

**🔥 Good luck, Commander. 10 errors stand between you and system restoration. Show them no mercy.**
