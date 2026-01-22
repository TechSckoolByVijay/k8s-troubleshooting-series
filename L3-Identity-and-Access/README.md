# 🔥 PROJECT PHOENIX – MISSION L3: THE FORTRESS
## **Kubernetes Identity & Access Control (RBAC)**

---

## 📡 MISSION BRIEFING

**Commander**, the Phoenix Fortress is under lockdown. The deployment is **online but crippled** — denied access to critical Kubernetes API operations due to **missing identity and permission configurations**.

This mission introduces a new threat level: **security and access control failures**. The pod cannot perform its duties because:
- ❌ **ServiceAccount missing** → Identity crisis
- ❌ **Memory limits too restrictive** → Resource starvation
- ❌ **Read-only filesystem** → Application crashes
- ❌ **RBAC permissions absent** → API access denied (403 Forbidden)

Your objective: **Establish proper identity, fix resource constraints, and grant necessary permissions** using Kubernetes RBAC (Role-Based Access Control).

🛡️ **This is defensive operations.** You're not just fixing crashes — you're configuring **who can do what** in the cluster.

---

## 🎯 MISSION OBJECTIVE

Deploy the **Phoenix Fortress** application to the `phoenix-system-3` namespace with:
- ✅ ServiceAccount created and assigned to the pod
- ✅ Memory limits sufficient for Python runtime
- ✅ Filesystem writable for application logs/temp files
- ✅ RBAC Role and RoleBinding granting necessary API permissions
- ✅ Pod status: **1/1 READY and Running**
- ✅ Application reporting API access **GRANTED** (not 403 Forbidden)

**Victory Condition:** The application can interact with the Kubernetes API to list pods in its namespace, demonstrating proper identity and permissions.

---

## 📂 MISSION FILES

| File | Description | Difficulty |
|------|-------------|------------|
| **01-phoenix-challenge.yaml** | 🌑 **Dark Mode** – 4 errors spanning identity, resources, and RBAC. No hints. | ⚡⚡⚡ |
| **02-phoenix-guided.yaml** | 🔦 **Intel Mode** – Error markers explaining each failure with fix guidance. | ⚡⚡ |
| **03-phoenix-solution-v1** | ✅ **Reference Solution** – Complete working configuration with RBAC. | Reference Only |

---

## 🛠️ TROUBLESHOOTING INTEL

### **Core Commands for This Mission**

| Command | Purpose |
|---------|---------|
| `kubectl -n phoenix-system-3 get pods` | Check pod status (look for ServiceAccount errors, OOMKilled) |
| `kubectl -n phoenix-system-3 describe pod <pod-name>` | Identify ServiceAccount issues, memory problems, permission errors |
| `kubectl -n phoenix-system-3 get sa` | List ServiceAccounts in the namespace |
| `kubectl -n phoenix-system-3 get role,rolebinding` | Verify RBAC resources exist |
| `kubectl -n phoenix-system-3 logs <pod-name>` | Check application logs for "Read-only file system" or API errors |

### **Advanced Recon (Optional)**

```bash
# Check ServiceAccount details
kubectl -n phoenix-system-3 describe sa phoenix-admin

# Verify Role permissions
kubectl -n phoenix-system-3 describe role phoenix-reader

# Verify RoleBinding associations
kubectl -n phoenix-system-3 describe rolebinding phoenix-read-binding

# Test API access from within pod
kubectl -n phoenix-system-3 exec <pod-name> -- curl -k https://kubernetes.default.svc/api/v1/namespaces/phoenix-system-3/pods

# Check pod security context
kubectl -n phoenix-system-3 get pod <pod-name> -o jsonpath='{.spec.securityContext}'
```

---

## 🚨 ERRORS COVERED IN THIS MISSION

This lab contains **4 critical errors** focused on identity and access control:

### **1️⃣ ServiceAccount Not Found**
- **Symptom:** Pod stuck in `Pending` state
- **Root Cause:** Deployment references `serviceAccountName: phoenix-admin` but the ServiceAccount doesn't exist
- **Detection:** `kubectl describe pod` shows: `"ServiceAccount phoenix-admin not found"`
- **Learning Goal:** Understanding ServiceAccount as pod identity in Kubernetes

**Fix Required:**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: phoenix-admin
  namespace: phoenix-system-3
```

### **2️⃣ Memory Limit Too Low (OOMKilled)**
- **Symptom:** Pod shows `OOMKilled` (Out Of Memory) and restarts repeatedly
- **Root Cause:** Memory limit set to `12Mi` (megabytes) — insufficient for Python runtime
- **Detection:** `kubectl describe pod` shows: `Last State: Terminated (Reason: OOMKilled)`
- **Learning Goal:** Proper resource limit configuration for different runtimes

**Fix Required:** Increase memory limit to `128Mi` or higher

### **3️⃣ Read-Only Filesystem**
- **Symptom:** Application logs show errors: `"Read-only file system"` or crashes during startup
- **Root Cause:** ConfigMap mounted with `readOnly: true` prevents Python from writing temp files
- **Detection:** `kubectl logs <pod>` shows file write errors
- **Learning Goal:** Volume mount permissions and application filesystem requirements

**Fix Required:** Set `readOnly: false` in volumeMount configuration

### **4️⃣ RBAC Permissions Missing (403 Forbidden)**
- **Symptom:** Pod runs successfully, but application reports `"403 FORBIDDEN - Identity cannot LIST pods"`
- **Root Cause:** ServiceAccount exists but has no Role or RoleBinding granting API permissions
- **Detection:** Application API response shows access denied
- **Learning Goal:** Kubernetes RBAC model (ServiceAccount → Role → RoleBinding)

**Fix Required:**
```yaml
# Create Role with permissions
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: phoenix-reader
  namespace: phoenix-system-3
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
---
# Bind Role to ServiceAccount
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: phoenix-read-binding
  namespace: phoenix-system-3
subjects:
- kind: ServiceAccount
  name: phoenix-admin
  namespace: phoenix-system-3
roleRef:
  kind: Role
  name: phoenix-reader
  apiGroup: rbac.authorization.k8s.io
```

---

## ✅ SUCCESS CRITERIA

Your mission is **COMPLETE** when you achieve:

| Checkpoint | Validation Command | Expected Output |
|------------|-------------------|-----------------|
| **ServiceAccount Exists** | `kubectl -n phoenix-system-3 get sa phoenix-admin` | ServiceAccount listed |
| **Pod Running** | `kubectl -n phoenix-system-3 get pods` | `phoenix-deployment-xxxxx   1/1   Running` |
| **No OOMKilled Events** | `kubectl -n phoenix-system-3 describe pod <pod>` | No "OOMKilled" in events |
| **No Filesystem Errors** | `kubectl -n phoenix-system-3 logs <pod>` | No "Read-only file system" errors |
| **Role Created** | `kubectl -n phoenix-system-3 get role` | `phoenix-reader` role exists |
| **RoleBinding Created** | `kubectl -n phoenix-system-3 get rolebinding` | `phoenix-read-binding` exists |
| **API Access Granted** | Application response | Shows API access as "GRANTED" (not 403) |

---

## 🎓 LEARNING OUTCOMES

By completing this mission, you will master:

- ✅ **ServiceAccount creation** and assignment to pods
- ✅ **Pod identity model** in Kubernetes (ServiceAccount as pod identity)
- ✅ **Resource limits tuning** (memory requirements for different runtimes)
- ✅ **OOMKilled troubleshooting** and memory pressure analysis
- ✅ **Volume mount permissions** (readOnly vs writable)
- ✅ **Kubernetes RBAC fundamentals** (Role, RoleBinding, ServiceAccount)
- ✅ **API permission debugging** (403 Forbidden vs 401 Unauthorized)
- ✅ **Security context configuration** (runAsUser, fsGroup)
- ✅ **Principle of least privilege** (granting only necessary permissions)

---

## 🚀 DEPLOYMENT INSTRUCTIONS

### **Mode 1: Challenge (Recommended for Learning)**
```bash
# Deploy the broken configuration
kubectl apply -f 01-phoenix-challenge.yaml

# Troubleshoot systematically
kubectl -n phoenix-system-3 get pods
kubectl -n phoenix-system-3 describe pod <pod-name>

# Create missing resources (ServiceAccount, Role, RoleBinding)
# Edit the YAML to fix resource limits and mount permissions

# Redeploy after fixes
kubectl apply -f 01-phoenix-challenge.yaml

# Verify each checkpoint
kubectl -n phoenix-system-3 get sa,role,rolebinding
kubectl -n phoenix-system-3 get pods
kubectl -n phoenix-system-3 logs <pod-name>
```

### **Mode 2: Guided (With Hints)**
```bash
# Deploy with inline error explanations
kubectl apply -f 02-phoenix-guided.yaml

# Read the inline comments for troubleshooting guidance
# Comments explain each error and provide fix directions
```

### **Mode 3: Solution (Reference)**
```bash
# Deploy the complete working configuration
kubectl apply -f 03-phoenix-solution-v1

# Verify all components
kubectl -n phoenix-system-3 get all
kubectl -n phoenix-system-3 get sa,role,rolebinding
```

---

## 🧹 CLEANUP

```bash
# Remove all resources when done
kubectl delete namespace phoenix-system-3
```

---

## 💡 COMMANDER'S TIPS

1. **ServiceAccount first:** Can't start without identity — fix this before anything else
2. **Watch for OOMKilled:** Memory limits for Python apps should be 128Mi+ minimum
3. **Read-only mounts:** ConfigMaps default to readOnly — applications often need write access
4. **RBAC is separate:** Even with a ServiceAccount, you need Role + RoleBinding for API access
5. **Test incrementally:** Fix identity → resources → filesystem → RBAC in that order
6. **Verify RBAC bindings:** Use `kubectl describe rolebinding` to confirm Subject → Role linkage
7. **Security context matters:** `runAsUser` and `fsGroup` affect file permissions
8. **Least privilege principle:** Only grant `get` and `list` on `pods` — nothing more

---

## 📚 REFERENCE DOCUMENTATION

- [Service Accounts](https://kubernetes.io/docs/concepts/security/service-accounts/)
- [Configure Service Accounts for Pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)
- [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [Managing Resources for Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Configure a Security Context for a Pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

---

## 🎯 TROUBLESHOOTING WORKFLOW

```
1. Check Pod Status
   └─ Pending? → ServiceAccount missing
   └─ OOMKilled? → Memory too low
   └─ Running but logs show errors? → Filesystem or permissions

2. Fix ServiceAccount
   └─ Create: kubectl apply -f serviceaccount.yaml

3. Fix Resource Limits
   └─ Edit deployment: increase memory to 128Mi

4. Fix Volume Mount
   └─ Set readOnly: false

5. Create RBAC Resources
   └─ Role (defines permissions)
   └─ RoleBinding (assigns Role to ServiceAccount)

6. Validate
   └─ kubectl get pods → 1/1 Running
   └─ kubectl logs → No errors
   └─ App response → API access granted
```

---

## 🛡️ RBAC COMPONENTS EXPLAINED

| Resource | Purpose | Example |
|----------|---------|---------|
| **ServiceAccount** | Identity assigned to pods | `phoenix-admin` |
| **Role** | Set of permissions (what actions on which resources) | Allow `get`, `list` on `pods` |
| **RoleBinding** | Connects ServiceAccount to Role | `phoenix-admin` gets `phoenix-reader` role |

**Flow:** Pod → ServiceAccount → RoleBinding → Role → API Permissions

---

**🔥 Good luck, Commander. The fortress walls are down. Restore identity, permissions, and security. Make the Phoenix unbreakable.**
