
**Google Cloud: Resources & Access Management**  
### **1. Resource Hierarchy**  
#### **Organization Node**  
**Explanation**:  
The Organization Node is the highest-level container in Google Cloud’s resource hierarchy, representing your entire company (e.g., `your-company.com`). All other resources (folders, projects) fall under this node.  

**Key Features**:  
- Automatically created if your company uses Google Workspace.  
- Requires Cloud Identity for non-Workspace users.  

**Example**:  
- A company with the domain `acme.com` has an organization node that encompasses all its Google Cloud resources.  
- **Special Roles**:  
  - **Organization Policy Admin**: Enforces rules like "All VMs must use internal IPs."  
  - **Project Creator**: Controls who can create projects (and manage budgets).  

---

#### **Folders**  
**Explanation**:  
Folders are used to group projects or departments (e.g., `Finance`, `HR`) for easier policy management. Policies applied to a folder automatically apply to all projects and subfolders within it.  

**Example**:  
- A folder named `/Finance/` contains projects like `budget-2023` and `payroll-system`.  
- A policy like "Deny assigning public IPs in `/Finance/`" applies to all VMs in these projects.  

---

#### **Projects**  
**Explanation**:  
Projects are isolated units that hold resources, enable APIs, and manage billing. Each project has:  
- **Project ID**: Globally unique and immutable (e.g., `prod-website-123`).  
- **Project Name**: User-defined and mutable (e.g., "Production Website").  

**Example**:  
- A project named `dev-game-server` enables the Compute Engine API and hosts a VM named `game-vm1`.  

---

#### **Resources**  
**Explanation**:  
Resources are actual services deployed within projects, such as VMs, storage buckets, or databases.  

**Example**:  
- A Compute Engine instance: `us-central1-a/game-vm1`.  
- A Cloud Storage bucket: `gs://game-assets-bucket`.  

---

### **2. IAM (Identity & Access Management)**  
#### **Policies**  
**Explanation**:  
IAM policies define **who** (users, groups, or service accounts) can **do what** (roles) on **which resources**. Policies inherit downward (e.g., from folders to projects).  

**Example**:  
- Policy: "`alice@company.com` has `roles/storage.admin` on `gs://finance-reports`."  
- Inheritance: A folder policy like "Deny SQL exports" applies to all projects under `/Finance/`.  

---

#### **Roles**  
**Explanation**:  
Roles bundle permissions to simplify access control.  

**Types**:  
- **Basic Roles**: Broad access (e.g., `roles/viewer` for read-only, `roles/owner` for full control).  
- **Predefined Roles**: Service-specific (e.g., `roles/compute.instanceAdmin.v1` to manage VMs).  
- **Custom Roles**: Tailored permissions (e.g., `roles/custom.logViewer` for log access only).  

---

#### **Deny Policies**  
**Explanation**:  
Deny policies override allow policies, blocking specific actions regardless of roles.  

**Example**:  
- "Deny `*.bigquery.datasets.delete` for all users in the `/Temp/` folder."  

---

### **3. Service Accounts**  
**Explanation**:  
Service accounts (SAs) are non-human identities used by applications or VMs to access Google Cloud resources. They authenticate via JSON key files.  

**Example**:  
- An SA named `data-pipeline-sa@project.iam.gserviceaccount.com` is assigned `roles/bigquery.dataEditor`, allowing a VM to write data to BigQuery.  

---

### **4. Cloud Identity**  
**Explanation**:  
Cloud Identity centralizes user and group management, syncing with existing systems like Active Directory.  

**Example**:  
- When an employee leaves, disabling `bob@company.com` in the Google Admin Console revokes all their Google Cloud access.  

---

### **5. Interaction Methods**  
#### **Cloud Console**  
- Web-based GUI for managing resources (e.g., deploying VMs via point-and-click).  

#### **Cloud SDK/Shell**  
- CLI tools like `gcloud` and `bq` (e.g., `gcloud compute instances list`).  

#### **APIs**  
- Programmatic control (e.g., Python script to auto-scale VMs using the Compute Engine API).  

#### **Mobile App**  
- Monitor resources and receive budget alerts on-the-go.  
