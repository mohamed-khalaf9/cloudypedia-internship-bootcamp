#### **Module Overview**  
This module covers three core areas of API security:  
1. **Content-Based Security**: Protection against malicious payloads.  
2. **Transport Security**: Securing data in transit (TLS).  
3. **Internal Security**: Safeguarding sensitive data within Apigee.  

---

### **1. Content-Based Attacks & Protection**  
#### **Threats**  
- **Content-Based Attacks**: Malicious payloads designed to:  
  - Leak/destroy data (e.g., SQL injection).  
  - Overwhelm parsers (e.g., deeply nested JSON/XML causing DoS).  
- **Legacy Services**: Vulnerable as they assumed internal/firewalled access.  

#### **Protection Policies**  
- **Input Validation**:  
  - Use `ExtractVariables` policy with **JSONPath/XPath** to parse payloads.  
  - Validate fields using conditions or JavaScript policies.  
  - Rewrite backend error messages for consistency.  
- **RegularExpressionProtection**:  
  - Blocks requests matching dangerous patterns (e.g., SQL keywords like `DROP TABLE`).  
  - Scans headers, URIs, payloads, and variables.  
  ```xml
  <Pattern>[{s}]^(delete){exec} {drop\s*table}</Pattern>  <!-- Blocks SQLi -->
  ```  
- **JSON/XMLThreatProtection**:  
  - Enforces structural limits **without parsing** (prevents parser-overload DoS):  
    - `ArrayElementCount`, `ContainerDepth`, `ObjectEntryCount` (JSON).  
    - `NodeDepth`, `AttributeCountPerElement` (XML).  
  ```xml
  <JSONThreatProtection>
    <ContainerDepth>10</ContainerDepth>  <!-- Rejects overly nested JSON -->
  </JSONThreatProtection>
  ```  

#### **Critical: Content-Type Handling**  
- Policies like `ExtractVariables` **require correct `Content-Type`** (e.g., `application/json`).  
- **Best Practice**: Enforce `Content-Type` via proxy conditions:  
  ```xml
  <Step>
    <Name>RF-ContentTypeInvalid</Name>
    <Condition>request.header.Content-Type != "application/json"</Condition>  <!-- Block non-JSON -->
  </Step>
  <Step>
    <Name>AM-SetContentTypeToJSON</Name>
    <Condition>request.header.Content-Type == null</Condition>  <!-- Default to JSON -->
  </Step>
  ```  

---

### **2. Transport Security (TLS)**  
#### **TLS Fundamentals**  
- **Purpose**: Encrypts HTTP traffic (→ HTTPS).  
- **OAuth Requirement**: All OAuth traffic **must** use TLS.  
- **Types**:  
  - **One-Way TLS**: Server validates itself to client (common in browsers).  
  - **Two-Way (Mutual) TLS**: Client and server mutually authenticate (Apigee ↔ backend).  

#### **TLS Implementation in Apigee**  
- **Incoming Traffic (Client → Apigee)**:  
  - Handled by Google Cloud load balancer.  
- **Outgoing Traffic (Apigee → Backend)**:  
  - Configured in `TargetEndpoint` using:  
    - **Truststore**: Validates backend certificates (required for one-way/two-way TLS).  
    - **Keystore**: Stores Apigee’s certificate/private key (required for two-way TLS).  
  ```xml
  <HTTPTargetConnection>
    <SSLInfo>
      <TrustStore>ref://backend-truststore</TrustStore>  <!-- One-way TLS -->
      <ClientAuthEnabled>true</ClientAuthEnabled>         <!-- Two-way TLS -->
      <KeyStore>ref://proxy-keystore</KeyStore>          <!-- Apigee's cert -->
    </SSLInfo>
  </HTTPTargetConnection>
  ```  
- **Keystore/Truststore Best Practices**:  
  - Use **references** (e.g., `ref://my-keystore`) to enable certificate rotation without downtime.  

#### **IP-Based Access Control**  
- **AccessControl Policy**: Restricts traffic by IP/CIDR ranges.  
  ```xml
  <AccessControl>
    <IPRules noRuleMatchAction="DENY">
      <MatchRule action="ALLOW">
        <SourceAddress mask="24">192.168.1.0</SourceAddress>  <!-- Allow /24 subnet -->
      </MatchRule>
    </IPRules>
  </AccessControl>
  ```  

---

### **3. Internal Security**  
#### **Cloud IAM & Roles**  
- **Resource Hierarchy**: Google Cloud Org → Folders → Projects → Apigee Org → Environments.  
- **Predefined Roles**:  
  - `Apigee Organization Admin`: Full access.  
  - `Apigee API Admin`: Create/test proxies.  
  - `Apigee Environment Admin`: Deploy proxies.  
- **Principle of Least Privilege**: Use custom roles for minimal permissions.  

#### **Data Masking**  
- **Purpose**: Hide sensitive data (e.g., passwords, CC numbers) in Apigee traces.  
- **DebugMask API**: Configured per environment using JSONPath/XPath:  
  ```bash
  PATCH /v1/organizations/{org}/environments/{env}/debugmask
  {
    "requestJSONPaths": ["$.password"],
    "variables": ["private.api_key"]
  }
  ```  
  Masks `password` in requests and `private.api_key` variables.  

#### **Private Variables**  
- **Syntax**: `private.{varname}` (e.g., `private.credentials`).  
- **Behavior**: Values masked in traces.  
  - ⚠️ **Caution**: Assigning `private.var` to a non-private variable exposes the value.  

#### **Key Value Maps (KVMs) vs. Property Sets**  
| **Feature**               | **KVMs**                          | **Property Sets**                     |  
|---------------------------|-----------------------------------|---------------------------------------|  
| **Encryption**            | ✅ Encrypted at rest              | ❌ Plaintext                          |  
| **Dynamic Updates**       | ✅ Via `KeyValueMapOperations`    | ❌ Static (requires redeployment)     |  
| **Scope**                 | Org/Env/Proxy                     | Env/Proxy                             |  
| **Sensitive Data Support**| ✅ (values → private variables)   | ❌ Avoid for secrets                  |  
| **Use Case**              | Backend credentials, API keys     | Static config (e.g., `loglevel=info`) |  

- **KVM Example**:  
  ```xml
  <KeyValueMapOperations name="KVM-GetCreds">
    <Get assignTo="private.backendPassword">  <!-- MUST assign to private var -->
      <Key>
        <Parameter>backend-service</Parameter>
        <Parameter>password</Parameter>
      </Key>
    </Get>
  </KeyValueMapOperations>
  ```  
- **Property Set Example**:  
  File: `config.properties` → `status_message=System operational`.  
  Access via: `propertyset.config.status_message`.  

---

### **Labs & Key Takeaways**  
1. **JSON Threat Protection Lab**: Enforce structural limits on JSON payloads.  
2. **Regex Threat Protection Lab**: Block SQLi/XSS patterns.  
3. **KVM Lab**: Store/retrieve backend credentials securely.  
4. **Data Masking Lab**: Hide PII in traces using `DebugMask` and private variables.  

#### **Core Principles**  
- **Defense-in-Depth**: Combine content, transport, and internal security.  
- **Zero Trust**: Validate all inputs; encrypt all traffic; limit internal access.  
- **Automated Security**: Use policies (not manual checks) for scalability.  
