#### **Module Overview**  
This module introduces Google Cloud's core infrastructure, covering:  
1. **Cloud Computing Fundamentals**: Definition and evolution.  
2. **Service Models**: IaaS vs. PaaS.  
3. **Global Network**: Design and scalability.  
4. **Sustainability**: Environmental initiatives.  
5. **Security**: Multi-layered protections.  
6. **Open Ecosystem**: Anti-lock-in philosophy.  
7. **Pricing**: Flexible billing and cost controls.  

---

### **1. Cloud Computing Fundamentals**  
#### **Definition (NIST Standard)**  
Cloud computing has **five essential traits**:  
1. **On-Demand Self-Service**: Provision resources (compute/storage/network) instantly via UI/API.  
2. **Broad Network Access**: Access resources globally over the internet.  
3. **Resource Pooling**: Providers allocate resources from shared pools (enables economies of scale).  
4. **Elasticity**: Scale resources up/down automatically with demand.  
5. **Pay-as-You-Go**: Pay only for consumed resources (no upfront costs).  

#### **Evolution of Cloud**  
- **1st Wave (Colocation)**: Rent physical data center space.  
- **2nd Wave (Virtualization)**: Virtual machines mimic physical hardware (e.g., VMware).  
- **3rd Wave (Containers)**: Fully automated, elastic infrastructure (pioneered by Google).  
  - **Key Insight**: Every company will compete through software and data.  

---

### **2. Cloud Service Models**  
| **Model**       | **Description**                                  | **Google Example**       | **Billing**              |  
|-----------------|--------------------------------------------------|--------------------------|--------------------------|  
| **IaaS**        | Raw compute, storage, networking resources.      | Compute Engine           | Pay for allocated resources. |  
| **PaaS**        | Managed platform; developers focus on code.      | App Engine               | Pay for actual usage.    |  
| **SaaS**        | Full applications (e.g., Gmail).                 | Google Workspace         | Subscription-based.      |  

#### **Trend**: Shift to **serverless** (e.g., Cloud Run) for zero infrastructure management.  

---

### **3. Google's Global Network**  
#### **Design Principles**  
- **Performance**: 100+ global caching nodes for low latency/high throughput.  
- **Scalability**: Largest network of its kind (billions in investment).  
- **Redundancy**: Multi-region/multi-zone architecture.  

#### **Structure**  
1. **Locations**: 7 geographic areas (Americas, Europe, Asia, etc.).  
2. **Regions**: Independent areas (e.g., `europe-west2` for London).  
3. **Zones**: Isolated failure domains within regions (e.g., `europe-west2-a`).  
   - **Best Practice**: Distribute resources across zones for fault tolerance.  
4. **Multi-Region Services**: e.g., Spanner databases replicate across regions for resilience.  

> 🌐 **Current Scale**: 127+ zones (growing); see [cloud.google.com/about/locations](https://cloud.google.com/about/locations).  

---

### **4. Sustainability**  
#### **Environmental Impact**  
- Data centers use **~2% of global electricity**.  
- Google’s initiatives:  
  - **Efficiency**: Seawater cooling in Finland (world-first).  
  - **Certifications**: First ISO 14001-certified data centers.  
  - **Goals**:  
    - **Carbon Neutral** (since 2007).  
    - **100% Renewable Energy** (achieved).  
    - **24/7 Carbon-Free Energy** (by 2030).  

---

### **5. Security**  
Google secures infrastructure across six layers:  
1. **Hardware**: Custom servers/chips; secure boot stack; physical security.  
2. **Service Deployment**: Encrypted RPCs between data centers.  
3. **User Identity**: Risk-based auth + U2F security keys.  
4. **Storage**: Encryption at rest (central keys + hardware support).  
5. **Internet**: Google Front End (GFE) enforces TLS/DoS protection.  
6. **Operations**:  
   - Intrusion detection (AI-driven).  
   - Employee U2F mandates + two-party code reviews.  
   - Vulnerability Rewards Program.  

> 🔒 **Learn More**: [cloud.google.com/security/security-design](https://cloud.google.com/security/security-design).  

---

### **6. Open APIs & Anti-Lock-In**  
- **Philosophy**: Avoid vendor lock-in via open standards.  
- **Examples**:  
  - **TensorFlow**: Open-source ML library.  
  - **Kubernetes/GKE**: Run microservices across clouds.  
  - **Google Cloud Observability**: Monitor multi-cloud workloads.  
- **Key Benefit**: Port workloads to/from Google Cloud freely.  

---

### **7. Pricing & Billing**  
#### **Core Principles**  
- **Per-Second Billing**: For Compute Engine, GKE, Dataproc.  
- **Discounts**:  
  - **Sustained Use**: Automatic discounts for long-running VMs.  
  - **Custom VMs**: Right-size vCPU/RAM to optimize costs.  

#### **Cost Controls**  
1. **Pricing Calculator**: Estimate costs ([cloud.google.com/products/calculator](https://cloud.google.com/products/calculator)).  
2. **Budgets & Alerts**: Set spend limits + notifications.  
3. **Quotas**:  
   - **Rate Quotas**: Limit API calls/time (e.g., 3,000 GKE calls/100 sec).  
   - **Allocation Quotas**: Cap resources (e.g., max 15 VPCs/project).  

---

### **Key Takeaways**  
1. **Cloud Advantage**: Agility, scalability, cost efficiency.  
2. **Google’s Edge**:  
   - Global low-latency network.  
   - Industry-leading sustainability.  
   - "Secure by design" infrastructure.  
3. **Cost Governance**: Use budgets, alerts, and quotas to prevent overspending.  



