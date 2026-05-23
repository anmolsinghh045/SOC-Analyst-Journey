##Day 02 — SOC Core Concepts, Security Teams & Human Attack Vectors
Last updated: Day 2 of the 30-day cybersecurity challenge · Difficulty: Intermediate

#📚 Table of Contents

1. SOC Core Concepts — People, Process, Technology
→ People: SOC L1 to CISO
→ Process: The 5W Framework
→ Technology: SIEM, EDR, Firewall & more
2. Security Department Structure
→ Red Team / Blue Team / GRC / CERT
3. Human Attack Vectors in SOC
→ Why humans are targeted
→ Defensive strategies

------------------------------------------------------------------------------------------------------------------------------------------------------------------

Section 01
SOC Core Concepts
A Security Operations Center doesn't just run on tools — it runs on three pillars that have to work together or the whole thing falls apart.
I like to think of it as a triangle. You break one side and the shape collapses. Let's go through each one properly.

👥 People — The Human Layer
Every alert that gets fired, every incident that gets contained — at some point a human being makes a decision. The SOC is structured in tiers,
and each tier has a completely different job. Here's how it actually looks from inside:

SOC Analyst L1
Alert triage, monitoring
→
SOC Analyst L2
Deep investigation
→
SOC L3 / IR Lead
Threat hunting, IR
→
SOC Manager
Ops, KPIs, team lead
→
CISO
Strategy & risk


L1 Analyst — The First Eyes
This is where you start. Your job is to watch dashboards, review SIEM alerts, and decide what's worth escalating. 90% of what you see will be false positives.
The skill is learning to spot the 10% that isn't. Typical tools: SIEM console, ticketing systems, threat intel feeds.

L2 Analyst — Going Deeper
L2 picks up the cases L1 flags. You're doing actual packet analysis, log correlation, malware behavior review. You're not just saying "this looks bad" — 
you're explaining why it's bad, what it touched, and what came next. This is where real investigation begins.

L3 / Incident Responder
The senior tier. You're either hunting threats proactively or leading an active incident. Forensics, memory analysis, timeline reconstruction —
this is high-pressure, high-skill work. L3s also write detection rules and tune alert logic for the whole SOC.

SOC Manager
Manages the people and the process. They own shift scheduling, metrics reporting (MTTD, MTTR), SLA compliance, and communication to leadership.
A good SOC manager removes friction so analysts can actually do their job. A bad one creates tickets instead of solving root causes.

CISO — Chief Information Security Officer
The CISO sits at the executive level. They're not in the weeds of individual alerts — they're defining risk appetite for the organization, 
signing off on security budgets, communicating risk to the board, and making sure every security program (SOC, GRC, red team, etc.) aligns 
with business goals. In smaller orgs, the CISO might still be hands-on. At enterprise scale, they're almost purely strategic.

⚡ Real Talk
A lot of people treat L1 like a throwaway role. That's wrong. The best threat hunters I've read about all say the same thing — you learn 
pattern recognition at L1. Every alert you close without understanding is a lesson you missed. Take L1 seriously even if it feels repetitive.

------------------------------------------------------------------------------------------------------------------------------------------------------------------


⚙️ Process — The 5W Framework
When a security incident happens, the SOC needs a structured way to ask the right questions fast. Randomly poking around logs doesn't scale. 
The 5W framework is the skeleton of every proper investigation. Think of it as your mental checklist before you write anything in the incident ticket.

Who
Identity
Which user, account, or attacker identity is involved? Internal or external?
What
Action
What action was taken? Login, file write, lateral move, data exfil?
When
Timestamp
Exact time and timezone. Correlate across systems. Time drift matters.
Where
Location
Source IP, hostname, physical location, network segment, cloud region.
Why
Motive
Intent inference — recon, persistence, exfil, disruption, or financial gain?
In practice, you won't answer all 5 immediately — that's fine. The framework tells you what's missing. If you can't answer "When"
yet, that's your next pivot. Log sources that might fill the gap. This is how you build a timeline instead of a pile of notes.



incident_investigation.md · example ticket structure
# Incident Ticket — Structured Investigation

## WHO
- User: jsmith@company.com (compromised credential suspected)
- Source IP: 192.168.10.44 → external relay: 45.89.125.11

## WHAT
- Lateral movement via SMB, attempted access to \DC01\SYSVOL
- Mimikatz signatures detected in memory (EDR alert #4421)

## WHEN
- First seen: 2024-03-15 02:17 UTC (off-hours — high suspicion)
- Alert fired: 02:19 UTC — 2-minute gap noted

## WHERE
- Origin host: WKSTN-044, VLAN 12 (Finance)
- Target: DC01, VLAN 1 (Core Infrastructure)

## WHY
- Likely credential harvesting for persistent domain access
- # Motive: internal reconnaissance or pre-ransomware staging

------------------------------------------------------------------------------------------------------------------------------------------------------------------


🛠️ Technology — The Tool Stack
People decide and processes guide, but technology is what actually generates the signal. Here's the core toolkit you'll encounter in a real SOC environment.
Don't just memorize what they are — understand why they exist and what gap they fill.

SIEM
Security Information & Event Management. Aggregates logs from across the entire environment and applies correlation rules to surface anomalies. Think of it as 
the SOC's brain — everything flows here. Common platforms: Splunk, Microsoft Sentinel, IBM QRadar, Elastic SIEM. Without a SIEM you're flying blind.

EDR
Endpoint Detection & Response. Lives on individual machines. Watches process trees, file system changes, network connections, and memory behavior in real time.
Unlike AV, it doesn't just block known signatures — it catches behavioral anomalies. CrowdStrike, SentinelOne, Microsoft Defender ATP are the big names here.

Firewall
Network perimeter control. A next-gen firewall (NGFW) goes beyond port/protocol filtering — it does application awareness, IPS, SSL inspection, and user-identity 
tagging. Palo Alto, Fortinet, Cisco Firepower are common. It's your first line of defense and the biggest source of network log data.

SOAR
Security Orchestration, Automation & Response. Automates repetitive SOC workflows — like auto-blocking an IP when a threat intel hit fires, or auto-enriching
an alert with geo-IP and VirusTotal data before it even reaches L1. Reduces analyst fatigue. Palo Alto XSOAR, Splunk SOAR, IBM Resilient.

IDS / IPS
Intrusion Detection/Prevention Systems. IDS watches and alerts; IPS actively blocks. Sits on the network and inspects traffic for known attack signatures and
behavioral anomalies. Snort and Suricata are the open-source standards — you'll see these in almost every SOC lab setup and many production environments.

Threat Intel Platform
Aggregates IOCs — IPs, domains, hashes, TTPs — from feeds like MISP, AlienVault OTX, Recorded Future, or VirusTotal. Feeds context into your SIEM and SOAR 
so alerts arrive with enrichment. Without threat intel, you're investigating in the dark every time.

------------------------------------------------------------------------------------------------------------------------------------------------------------------


Section 02
Security Department Structure
Security is not one monolithic team. In any mature organization it's broken into specialized functions that operate differently but feed into each other.
Here's the honest breakdown:

🔴 Red Team
Adversary Simulation
Red team's job is to think and act like the enemy. They're not doing one-off pentests on a schedule — they're running persistent, stealthy campaigns that
simulate a real threat actor over days or weeks. They use real attacker TTPs (MITRE ATT&CK is their playbook), stay undetected as long as possible, and at
the end they hand the blue team a report of exactly how they got in, how far they got, and what would have caught them. If the red team had an easy time, 
that's a blue team problem that needs fixing.

Penetration Testing Adversary Emulation Social Engineering Physical Security MITRE ATT&CK
🔵 Blue Team
Defense & Detection
Blue team is the defender side — this includes the SOC, incident responders, threat hunters, and the people who build and tune the detection rules.
They're the ones keeping the lights on. A strong blue team doesn't just react to alerts — they proactively hunt for threats that haven't triggered an alert yet,
and they use red team findings to write better detections. The goal is to get MTTD (mean time to detect) as low as possible and 
MTTR (mean time to respond) even lower.

SOC Operations Threat Hunting Incident Response Detection Engineering Digital Forensics
⚖️ GRC — Governance, Risk, and Compliance
The Policy & Risk Layer
GRC doesn't write exploit code and doesn't hunt threats. They write the rules everyone else has to follow. They're translating regulatory requirements
(ISO 27001, NIST, SOC 2, GDPR, PCI-DSS) into actual internal controls, policies, and audit evidence. They also run risk assessments to help leadership 
understand where money should go in the security budget. People underestimate GRC — without it, even the best technical security team is operating withou
t governance and will get wrecked in a compliance audit or regulatory review.

ISO 27001 NIST CSF Risk Assessment Audit Management Policy Writing
🚨 CERT — Computer Emergency Response Team
Crisis Response Unit
CERT (also called CSIRT — Computer Security Incident Response Team) is the specialized function that takes over when things go seriously wrong.
We're not talking about a routine phishing alert here — CERT activates during major breaches, ransomware deployments, nation-state intrusions, 
or critical infrastructure events. They coordinate response across technical teams, legal, PR, and leadership simultaneously.
National CERTs (like CERT-In in India or US-CERT) also handle cross-organizational incident coordination and publish advisories for newly discovered threats
. Some organizations treat IR and CERT as the same function; larger enterprises keep them separate.

Team	Core Function	When They Activate	Key Output

Red Team	Offensive simulation	Scheduled campaigns, purple team exercises	Attack path report, detection gaps
Blue Team / SOC	Monitor & respond	Continuously (24/7 in mature orgs)	Incident tickets, detection rules, threat intel
GRC	Policy & compliance	Audits, regulatory changes, new vendor onboarding	Risk register, policy docs, audit reports
CERT / CSIRT	Major incident response	Declared incidents, critical CVE exploitation, breach	Incident reports, containment plans, advisories

------------------------------------------------------------------------------------------------------------------------------------------------------------------


Section 03
Human Attack Vectors in SOC
This is the one that doesn't get enough attention in technical training. You can have the best SIEM in the world, EDR on every endpoint, and a fully
staffed SOC — and a single well-crafted phishing email can still get through because a human made a decision under pressure. Here's why humans are the
target, and more importantly, how we stop it.

🎯 Why Humans Are Targeted
Cognitive Overload
Security analysts deal with thousands of alerts per day. That volume creates mental fatigue. Attackers know that alerts fired at 3am or during a 
high-pressure incident window are less likely to get scrutinized properly. Decision quality drops when people are tired, rushed, or dealing with 
competing priorities.
Authority Bias
People naturally comply with instructions that appear to come from authority figures — an email from the "CEO" asking you to approve a wire transfer,
or a call claiming to be from IT asking for your credentials to "fix a problem." This is BEC (Business Email Compromise) and vishing in a nutshell.
It works because it exploits social conditioning that exists outside the workplace too.
Urgency & Fear Triggers
The message that says "your account will be locked in 10 minutes" or "we've detected unauthorized activity — verify now" is engineered to short-circuit
rational thinking. Urgency suppresses skepticism. Attackers craft scenarios where the cost of inaction feels higher than the risk of clicking a link.
Insider Threat
Not every threat comes from outside. A disgruntled employee, a contractor with too much access, or someone who's been socially engineered into becoming
an unintentional insider — all of these are real scenarios the SOC has to account for. Insider threats are statistically harder to detect because the 
activity often starts within normal behavioral baselines.
Over-trust in Tools
Ironically, technology creates human vulnerability. When analysts trust tools too much — "if the SIEM didn't fire an alert, we're fine" — attackers 
exploit that assumption by living in legitimate processes (LOLBins), using signed certificates, or operating within thresholds that don't trigger rules.
This is why threat hunting exists: not every bad thing makes noise.
🛡️ How to Defend — Human-Layer Security
Security Awareness Training
Not the annual checkbox compliance video. Real training means simulated phishing campaigns, tabletop exercises, and training that's tailored to how your 
specific organization gets targeted. The goal is to build muscle memory — the kind of reflex that makes someone pause before clicking even when they're in a hurry.

Zero Trust Architecture
Don't trust, always verify. No implicit trust based on being inside the network. Every access request gets authenticated, authorized, and continuously validated.
Even if an attacker compromises a user, they can't move laterally because every resource requires fresh verification. This limits blast radius significantly.

Least Privilege Access
No user or system should have more access than they need for their specific job. Regular access reviews, role-based access control (RBAC), and
just-in-time (JIT) access provisioning. A compromised L1 analyst account with limited permissions is a much smaller problem than one with admin
rights across the domain.

UEBA — Behavioral Analytics
User and Entity Behavior Analytics builds a baseline of normal activity for every account in the environment. When behavior deviates — login from a new country,
mass file access, off-hours activity — the system flags it regardless of whether credentials were stolen or the account is legitimate. Catches what rules-based
detection misses.

MFA Everywhere
Multi-factor authentication is the single highest ROI control against credential theft. Phishing can steal your password but it's significantly harder to steal
your MFA token simultaneously. Phishing-resistant MFA (hardware keys like YubiKey, FIDO2) goes even further against real-time phishing proxy attacks.

Incident Response Playbooks
Humans make better decisions under pressure when they have documented procedures. Playbooks remove the need to improvise during an active
incident — you follow the steps, escalate at the right points, and don't panic-delete logs or accidentally tip off the attacker by locking an account too early.
Practice them with tabletop exercises.

⚠️ Key Insight — The Weakest Link Problem
There's a common phrase in security: the human is the weakest link. I think that framing is wrong — or at least incomplete. 
A poorly-trained human in a poorly-designed system is the weakest link. The same person in a well-structured environment with proper tooling, clear processes, and realistic training is actually your most adaptive detector. No algorithm catches a suspicious gut feeling from an experienced analyst. Build for human strength, not just human limitation.

------------------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------------------------------------------------------


Quick Reference Cheatsheet
Concept	One-line definition	Why it matters in SOC
SIEM	Centralized log aggregation + correlation engine	Primary alert generation source for L1/L2
EDR	Behavioral endpoint monitoring + response	Catches fileless malware, lateral movement, LOLBins
SOAR	Workflow automation for SOC processes	Reduces manual work, speeds up MTTR
UEBA	Baseline user behavior + anomaly detection	Detects insider threats and compromised accounts
MTTD	Mean time to detect an intrusion	Key SOC efficiency metric — lower is better
MTTR	Mean time to respond and contain	Measures containment speed post-detection
BEC	Business Email Compromise	Top financial attack vector targeting humans
LOLBins	Living off the land binaries (legit tools used maliciously)	Evade signature-based detection
TTP	Tactics, Techniques, and Procedures	MITRE ATT&CK framework language for attacker behavior
JIT Access	Just-in-time privilege provisioning	Reduces standing privilege attack surface
✅ Day 2 Complete

------------------------------------------------------------------------------------------------------------------------------------------------------------------

Tomorrow (Day 3) we'll get into network security fundamentals — OSI model from a security lens, common network-layer attacks, and how traffic analysis maps to the SOC workflow. If you want to get ahead, read up on Wireshark basics and the TCP/IP handshake process.

Written as part of a personal 30-day SOC & cybersecurity learning challenge. Notes are based on hands-on labs, research, and real-world SOC documentation patterns.

soc blue-team threat-detection security-awareness incident-response
