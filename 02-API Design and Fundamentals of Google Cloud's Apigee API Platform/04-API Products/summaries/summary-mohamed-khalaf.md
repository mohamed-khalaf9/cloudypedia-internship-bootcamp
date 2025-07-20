#### **Module Overview**  
This module covers API products, their role in API ecosystems, and best practices for REST API responses. Key topics include:  
1. **API Products**: Bundling APIs for developer consumption.  
2. **API Keys & Security**: Controlling access via `VerifyAPIKey` policy.  
3. **Product Strategy**: Designing evolvable API products.  
4. **REST Responses**: HTTP status codes and pagination.  

---

### **1. API Products: Fundamentals**  
#### **What is an API Product?**  
- A **bundle of API operations** (e.g., "Retail API Product" includes `GET /products`, `POST /cart`, `POST /checkout`).  
- **Controls access** and **service tiers**:  
  - Read-only vs. read-write access.  
  - Internal vs. external consumers.  
  - Free vs. premium plans (monetization).  
- **Custom Attributes**: Variables injected into API proxies for dynamic control:  
  ```yaml
  service-plan: premium       # Toggles features
  rate-limit: 100/req-per-sec # Controls traffic
  access-type: internal       # Restricts data exposure
  ```  

#### **Publishing Workflow**  
1. **Package APIs** into products (e.g., bundle inventory and order APIs).  
2. **Attach Custom Attributes** to products/operations.  
3. **Developers** register apps on a developer portal.  
4. **Apps** receive credentials:  
   - **Consumer Key**: Public API key (e.g., `ABC123`).  
   - **Consumer Secret**: Private secret (e.g., `XYZ789`).  
5. **Apps call APIs** using the API key:  
   ```http
   GET /inventory?apikey=ABC123
   ```  

#### **Entities & Relationships**  
| **Entity**         | **Purpose**                                  | **Custom Attributes**       |  
|--------------------|----------------------------------------------|-----------------------------|  
| **API Product**    | Bundle operations (e.g., "Gold Tier")        | `service-plan=premium`      |  
| **Developer**      | App creator (e.g., "Acme Corp")              | `partner-tier=platinum`     |  
| **App**            | Consumer of APIs (e.g., "Acme Shopping App") | `region=NA`                 |  
| **Credentials**    | `{consumer_key, consumer_secret}`            | N/A                         |  

---

### **2. Securing APIs with API Keys**  
#### **`VerifyAPIKey` Policy**  
- **Validates API keys** in requests (header/query param).  
- **Rejects invalid keys** early in the proxy flow.  
- **Populates variables** for app/developer/product attributes.  
- **Configuration**:  
  ```xml
  <!-- Policy Definition -->
  <VerifyAPIKey name="VK-Auth">
    <APIKey ref="request.header.apikey"/>  <!-- Checks "apikey" header -->
  </VerifyAPIKey>

  <!-- Proxy Attachment -->
  <ProxyEndpoint>
    <PreFlow>
      <Request>
        <Step><Name>VK-Auth</Name></Step>  <!-- Runs first -->
      </Request>
    </PreFlow>
  </ProxyEndpoint>
  ```  

#### **Post-Validation Variables**  
Format: `verifyapikey.{policy_name}.{attribute}`  
- **App Attributes**:  
  ```bash
  verifyapikey.VK-Auth.app.name = "Acme App"
  ```  
- **Developer Attributes**:  
  ```bash
  verifyapikey.VK-Auth.developer.email = "dev@acme.com"
  ```  
- **Product Attributes**:  
  ```bash
  verifyapikey.VK-Auth.apiproduct.service-plan = "premium"
  ```  

---

### **3. API Product Strategy**  
#### **Product vs. Project Mindset**  
| **Aspect**       | **Project**                  | **Product**                     |  
|------------------|------------------------------|---------------------------------|  
| **Focus**        | Deliverables/deadlines       | Business outcomes               |  
| **Lifespan**     | Fixed end date               | Evolves indefinitely            |  
| **Adaptability** | Rigid scope                  | Adjusts to market needs         |  

#### **Evolution Example: Retail Inventory APIs**  
1. **Phase 1 (Internal Team)**:  
   - Product: `FullAccess` → All inventory/restocking operations.  
2. **Phase 2 (Internal Read-Only)**:  
   - Product: `InternalRO` → Only `GET` operations (no writes).  
3. **Phase 3 (External Partners)**:  
   - Product: `Silver` → `GET /in-stock` (basic access).  
   - Product: `Gold` → Adds `GET /stock-dates` (premium feature).  
4. **Key Insight**:  
   - Add tiers/operations **without backend changes** using Apigee.  

---

### **4. REST API Responses**  
#### **HTTP Status Codes**  
| **Code** | **Meaning**               | **Use Case**                                  |  
|----------|---------------------------|----------------------------------------------|  
| `200 OK` | Success                   | Standard response for successful requests.    |  
| `201 Created` | Resource created      | After successful `POST` (e.g., new order).   |  
| `400 Bad Request` | Client error        | Malformed input (e.g., missing parameters).  |  
| `401 Unauthorized` | Auth required       | Missing/invalid credentials.                 |  
| `403 Forbidden` | Insufficient perms  | Valid credentials but no access.             |  
| `404 Not Found` | Resource missing    | Mask existence for security (`403` → `404`). |  
| `429 Too Many Requests` | Rate limiting | Too many calls (Apigee Spike Arrest).        |  
| `500 Server Error` | Unexpected issue   | Avoid leaking backend details (e.g., DB IPs).|  

#### **Response Best Practices**  
- **Good**:  
  ```json
  400 Bad Request
  {
    "error": "Missing 'q' parameter",
    "docs": "https://api.acme.com/guide"
  }
  ```  
- **Bad** (security leaks):  
  ```json
  500 Server Error
  {
    "error": "MySQL error at 10.3.4.33"  // Exposes internal IP/tech
  }
  ```  

#### **Pagination**  
- **Use Cases**: Large datasets (e.g., user listings, search results).  
- **Methods**:  
  1. **Offset/Limit**:  
     ```http
     GET /users?offset=20&limit=10
     ```
     ```json
     {
       "resources": [ ... ],
       "pagination": {
         "offset": 20,
         "count": 10,
         "totalResults": 245
       }
     }
     ```  
  2. **Value-Based (Cursor)**:  
     ```http
     GET /users?last_id=user123&limit=10  // Stable across data changes
     ```  

---

### **Labs & Key Takeaways**  
1. **API Key Lab**:  
   - Add `VerifyAPIKey` to a retail proxy.  
   - Create products/apps/developers and test access control.  
2. **Custom Attributes Lab**:  
   - Use `service-plan=premium` to enable premium features dynamically.  
3. **Core Principles**:  
   - **Product-First Mindset**: Design APIs for evolvability.  
   - **Defense-in-Depth**: Validate keys, mask errors, paginate data.  
   - **Developer Experience**: Clear docs, useful errors, consistent status codes.  
