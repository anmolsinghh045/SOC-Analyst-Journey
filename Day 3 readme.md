# Day 3: SOC Architecture, Attack Surface, and Infrastructure Security

🎯 Learning Outcomes
By the end of this module, I can:

Evaluate and contrast SOC Operating Models to choose the best defense blueprint.

Map out the entire Attack Surface, from human-centric spear-phishing to complex supply chain breaches and zero-day 
lifecycles.

Differentiate between core AAA flaws (Authentication vs. Authorization) and identify infrastructure misconfigurations.

Defend enterprise infrastructure across three primary tiers: Endpoints (AV to XDR), On-Premise Network Devices,
and Cloud Environments.

_______________________________________________________________________________________________________________________________________________________________________________

🏢 1. Security Operations Center (SOC) Operating Models
Choosing a SOC model dictates how an organization detects, triages, and responds to threats. It balances cost, data control, 
and expertise.

🏢 In-House (Dedicated) SOC
Data Privacy: Maximum. Your security telemetry, raw logs, and sensitive data never leave the enterprise boundary.

Cost Profile: High CapEx & OpEx. It requires heavy, upfront, and ongoing investments in enterprise tooling, SIEM licenses,
infrastructure, and full 24/7 staffing rotations.

Threat Context: Deep. The analysts are internal employees who understand your specific business logic, true asset criticalities, 
and the "tribal knowledge" of your network layout.

Scalability: Slow. Scaling up your coverage or shift size is entirely limited by your internal hiring pipelines, budget approvals, 
and onboarding/training times.

🤝 MSSP (Fully Outsourced)
Data Privacy: Low. Your security telemetry and logs must be sent outbound to a multi-tenant, third-party platform shared with other companies.

Cost Profile: Predictable OpEx. It operates on fixed, predictable subscription or consumption-based pricing models, turning security into an
operational expense.

Threat Context: Shallow. External analysts rely heavily on generic playbooks and automated alerts; they lack deep context regarding which of 
your internal systems are truly vital to daily business operations.

Scalability: Instant. If your monitoring needs grow or you add new infrastructure, the provider can scale your monitoring tiers and coverage up 
or down on demand.

🌗 Hybrid (Co-Sourced)
Data Privacy: High. High-severity or highly sensitive logs can be kept local, while standardized, sanitized alerts are sent out to the partner 
for initial triage.

Cost Profile: Balanced. Features a scalable base cost for the outsourced monitoring, backed by a leaner, highly specialized internal team for escalations.

Threat Context: Blended. The outsourced partner handles the heavy lifting of sorting through massive volumes of noise (Tier 1/2 alert fatigue), 
while your internal team applies native business context to critical escalations.

Scalability: Flexible. You can easily and quickly scale your Tier 1 and Tier 2 coverage limits through your external partner without overloading 
your internal engineers.

_______________________________________________________________________________________________________________________________________________________________________________


🦹 2. Modern Attack Surface & Vectors
A. Attack Vectors & Human-Oriented Exploits
Phishing & Spear-Phishing: Mass delivery vs. highly targeted delivery leveraging OSINT (Open Source Intelligence) to harvest organizational 
hierarchies and system details.

Business Email Compromise (BEC): Spoofing executive identities to manipulate financial transactions or access rights without deploying malware.

Watering Hole Attacks: Compromising a trusted, third-party website frequently visited by the specific target demographic.

B. Supply Chain Attacks
Attackers compromise upstream components to breach downstream targets.

Software Supply Chain: Injecting malicious code into open-source libraries or signed software updates.

Real-Life Example: SolarWinds (2020). Threat actors compromised the SolarWinds build system, injecting a backdoor called SUNBURST into legitimate, 
digitally signed updates of the Orion network management software, infecting thousands of downstream enterprise and government networks.

C. The Zero-Day Lifecycle
A Zero-Day vulnerability is an unpatched flaw unknown to the vendor.


Vulnerability Introduced  
        ↓  
Threat Actor Discovers Vulnerability  
        ↓  
Zero-Day Exploit Developed  
        ↓  
Attack Executed / Detection Begins  
        ↓  
Vendor Investigation & Notification  
        ↓  
Security Patch Released  
        ↓  
Systems Updated / Risk Mitigated
_______________________________________________________________________________________________________________________________________________________________________________


🔒 3. Vulnerability Management & Common Misconfigurations
Authentication vs. Authorization
💡 The Gold Standard Distinction:

Authentication (AuthN): Who are you? (Verifying identity via passwords, MFA, biometrics).

Authorization (AuthZ): What are you allowed to do? (Verifying permissions via RBAC or ACLs after identity is confirmed).

Broken Authentication: Allows attackers to bypass or compromise identity checks (e.g., credential stuffing, session fixation).

Broken Authorization (IDOR - Insecure Direct Object References): Occurs when an application provides direct access to objects based on 
user-supplied input.

Example: Changing the URL parameter from site.com/view_profile?id=1001 to id=1002 allows a user to view another person's private data
because the system fails to validate authorization rules.

Common Infrastructure Misconfigurations
Default Credentials: Leaving administrative portals set to factory settings (admin/admin).

Unnecessary Exposed Services: Leaving hazardous ports like RDP (3389) or SMB (445) open to the public internet.

Permissive Cloud Storage: S3 buckets or Azure Blobs configured with public read access, exposing sensitive PII or configuration dumps.

_______________________________________________________________________________________________________________________________________________________________________________


🛡️ 4. Enterprise Infrastructure & Security Architecture
A. Endpoint Security Evolution
Endpoints are the primary battleground for enterprise initial access.


XDR (Extended Detection & Response)  
→ Correlates endpoint, network, cloud, and security telemetry  
        ↓  
EDR (Endpoint Detection & Response)  
→ Behavioral analysis, continuous monitoring, live response  
        ↓  
EPP (Endpoint Protection Platform)  
→ Antivirus, malware prevention, sandboxing, local firewall

Antivirus (AV): Legacy signature-based detection. Fails against polymorphic malware or fileless attacks.

Endpoint Protection Platform (EPP): Preventive suite deployed on endpoints combining signature matching, basic heuristics, and local firewalls.

Endpoint Detection & Response (EDR): Focuses on continuous monitoring and behavioral visibility. Records system states, execution trees, 
network connections, and allows remote isolation and live forensics.

Extended Detection & Response (XDR): Breaks down silos by ingesting and correlating telemetry across endpoints, network infrastructure,
email security, and cloud workloads into a single analytical pane.

B. Network Architecture & Security Controls
Routers & Switches: Direct traffic; secured using Access Control Lists (ACLs), Port Security, and disabling unused interfaces.

Firewalls: Statefully inspect traffic passing between distinct security zones (e.g., DMZ to Internal LAN).

IDS / IPS: Intrusion Detection alerts on malicious signatures or behavior; Intrusion Prevention sits inline to actively drop or reset malicious connections.

VPNs & Proxies: VPNs encrypt traffic from remote points into the network boundary. Proxies act as intermediaries for outbound traffic,
filtering web content and inspecting SSL/TLS traffic.

Load Balancers: Distribute incoming application traffic across redundant servers. They act as a security buffer against Layer 7 DDoS attacks.

C. Cloud Architecture & The Shared Responsibility Model
Cloud computing abstracts physical hardware but introduces configuration complexities.

Identity & Access Management (IAM): The modern cloud perimeter. Dictates identities, roles, and granular policies using Least Privilege principles.

Core Pillars: Compute (Virtual Machines/Containers), Storage (Object/Block), Networking (VPCs, Security Groups), and Logging (CloudTrail/Activity Logs).

Here is the breakdown of the Shared Responsibility Model layers without using a table layout:

🏠 On-Premise (You Manage Everything)
In an on-premise environment, the Customer is 100% responsible for every single layer of the stack. This includes securing data and access control,
maintaining applications, patching the operating system, configuring network devices, and ensuring the physical security of the building and server racks.

🧱 Infrastructure as a Service (IaaS)
With IaaS (like AWS EC2 or Azure VMs), the provider hands you the raw virtualized infrastructure.

Customer Responsibilities: Securing Data & Access, managing and patching Applications, configuring and hardening the Operating System,
and managing Network Controls (like setting up security groups and firewall rules within the cloud environment).

Provider Responsibilities: The cloud provider takes care of the Physical Security of the data centers, environmental controls, and the
underlying physical hardware/hypervisor layer.

⚙️ Platform as a Service (PaaS)
With PaaS (like AWS Elastic Beanstalk or Azure SQL Database), the provider abstracts away the operating system and infrastructure management
so you can focus entirely on code.

Customer Responsibilities: Securing Data & Access and managing the custom Applications you deploy onto the platform.

Provider Responsibilities: The cloud provider manages the Operating System patches, underlying Network Controls, and all Physical Security of the infrastructure.

💻 Software as a Service (SaaS)
With SaaS (like Microsoft 365, Salesforce, or Google Workspace), the provider delivers a fully functional, ready-to-use software application over the internet.

Customer Responsibilities: Even in a fully outsourced software environment, the customer always retains ownership and responsibility for their own Data & Access 
(managing user identities, permissions, multi-factor authentication, and data classification).

Provider Responsibilities: The software vendor is responsible for securing the Applications, maintaining the underlying Operating System, configuring Network Controls,
and enforcing all Physical Security protocols.

🖼️ Architecture & Implementation Checkpoints
Visualizing Network Zoning (DMZ Setup)
Below is a standard 3-tier perimeter architecture separating public access from critical data stores.

x
Public Internet  
        ↓  
External Firewall  
        ↓  
DMZ (Web Proxy, Load Balancer)  
        ↓  
Internal Firewall  
        ↓  
Internal Network (AD, Application Tier)  
        ↓  
Secure Data Zone (Database Cluster)

_______________________________________________________________________________________________________________________________________________________________________________


  # ⚡ Quick Revision & Flashcards
  
What is the primary risk of software supply chain attacks? They subvert trust. Security defenses trust signed, legitimate software updates from authorized vendors, 
allowing malicious code to slide past the outer defenses uninspected.

AuthN vs AuthZ in one sentence: Authentication proves who you are; Authorization determines what you can do.

Why can't signature-based Antivirus stop zero-days? Signature engines look for a known static hash or pattern matching a known threat. Since a zero-day is entirely new, 
its signature does not exist in any database until after it has been discovered and documented.

Who secures the underlying host virtualization layer in an Amazon EC2 or Azure VM instance? The Cloud Service Provider (CSP) under the Shared Responsibility Model. 
The customer is responsible for patching the guest operating system installed inside that VM.
