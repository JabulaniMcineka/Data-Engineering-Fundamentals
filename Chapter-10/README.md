# 📚 Chapter 10 - Security and Privacy
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 10 covers **security and privacy** — a critical undercurrent that runs through every stage of the data engineering lifecycle. Security is not an afterthought. It must be the first consideration at every step.

> **Key idea:** The best way to protect sensitive data is to avoid collecting it in the first place. If you don't need the data downstream, don't ingest it.

---

## ⚠️ Why Security Matters So Much

- Data engineers handle sensitive data, credentials, and access **every single day**
- One security breach or data leak can destroy a business, career, and reputation
- Most breaches are caused by **human error** — not sophisticated attacks
- Privacy is increasingly a **legal obligation** with significant penalties

**Key privacy regulations:**

| Regulation | Region | Domain |
|---|---|---|
| **FERPA** | USA | Education records |
| **HIPAA** | USA | Health information |
| **GDPR** | Europe | All personal data |
| **CCPA** | California, USA | Consumer data |
| **POPIA** | South Africa | Personal information |

Penalties for violations can be devastating — fines, lawsuits, and permanent reputational damage.

---

## 👥 PILLAR 1: People

> **The weakest link in security is always a human being.**

### The Power of Negative Thinking
- Positive thinking blinds us to disaster scenarios
- Negative thinking = actively imagining what could go wrong and preparing for it
- Ask: What are the worst-case scenarios for this pipeline or storage system?
- Assume you are always a target — bots and actors are always trying to infiltrate

### Always Be Paranoid
- When someone asks for your credentials — **always verify before acting**
- Confirm requests through a second channel (phone call, separate chat)
- A quick confirmation call is cheaper than a ransomware attack
- Trust nobody at face value when it comes to credentials or sensitive data

### Ethical Responsibility
- If you are uncomfortable with how sensitive data is being handled, **raise it**
- You are the first line of defence for privacy and ethics in your organisation
- Ensure your work is both legally compliant AND ethically sound

---

## ⚙️ PILLAR 2: Processes

### Security Theater vs Security Habit

| Security Theater | Security Habit |
|---|---|
| Annual policy review nobody reads | Monthly security training baked into culture |
| Checking compliance boxes (SOC-2, ISO 27001) | Thinking through real attack scenarios |
| Hundreds of pages nobody follows | Simple, practical rules everyone actually follows |
| Compliance-driven | Culture-driven |

> 💡 **Pursue the spirit of security, not just the letter of compliance.** Everyone must own security — it is not just the security team's job.

### Active Security
- Research successful phishing attacks and think through your own vulnerabilities
- Consider internal threats — employees with access who might misuse data
- Run through incident response plans regularly — don't wait for a real breach

### Principle of Least Privilege

```
WRONG: Give everyone admin access — easier to manage
RIGHT: Give each person only what they need for the task, remove it when done
```

Apply to:
- **Human users** — specific IAM roles only
- **Service accounts** — scoped to specific tasks
- **Data access** — table, column, row, and cell level

**Broken glass process:**
- Most sensitive data is locked away
- Accessing it requires emergency approval from at least two people
- Access is immediately revoked once the work is done
- All access is logged and audited

### Shared Responsibility in the Cloud

```
Cloud Provider is responsible for:         You are responsible for:
- Physical data center security            - Application security
- Hardware and infrastructure              - Data access controls
- Network infrastructure                   - Configuration of cloud services
- Core cloud service security              - Secrets management
                                           - Encryption settings
                                           - IAM policies
```

> Most cloud breaches are caused by **end user misconfiguration** — not cloud provider failures.

### Always Back Up Your Data
- Back up regularly — for disaster recovery AND ransomware protection
- Test the restoration process — an untested backup is not a backup
- Encrypt all backups
- Store backups in a separate location from production data

---

## 💻 PILLAR 3: Technology

### Patch and Update Systems
- Security vulnerabilities are discovered constantly in software and dependencies
- Always patch and update operating systems and software promptly
- Automate builds and set alerts for new releases and vulnerabilities
- SaaS and cloud-managed services often handle updates automatically — verify this

### Encryption

**Encryption at Rest:**
- Full disk encryption on all company laptops
- Server-side encryption for all cloud storage (S3, databases, object storage)
- Encrypt all backups
- Application-level encryption for the most sensitive fields

**Encryption in Transit:**
- Always use HTTPS — never plain HTTP
- Never use FTP over public internet — use SFTP or SCP instead
- Ensure encryption keys are properly managed — bad key management is a major leak source
- Even public data should use encrypted transport to prevent man-in-the-middle attacks

### Logging, Monitoring, and Alerting

Monitor these areas and set up automated alerts:

| Area | What To Watch For |
|---|---|
| **Access logs** | Unusual access patterns, new unrecognised users, access to restricted resources |
| **Resource usage** | Unexpected spikes in CPU, memory, disk, I/O |
| **Billing** | Unexpected cost spikes — could indicate unauthorised resource usage |
| **Excess permissions** | Users or service accounts with unused permissions |

**Best practices:**
- Set up a security monitoring dashboard visible to the whole data team
- Combine access, resource, and billing monitoring for a cross-sectional view
- Have an incident response plan and practice it regularly
- Use automated anomaly detection where possible

### Network Access

**Common terrible practices to avoid:**
- Publicly accessible S3 buckets with sensitive data ❌
- EC2 instances with SSH open to `0.0.0.0/0` (all IPs) ❌
- Databases open to all inbound connections ❌

**Good practices:**
- Restrict inbound connections to specific trusted IP addresses and ports
- Never broadly open connections for convenience
- Use encrypted connections for all cloud and SaaS access
- Avoid public WiFi without a VPN

**Zero trust vs Hardened perimeter:**

| Model | Trust Model | Best For |
|---|---|---|
| **Zero trust** | Trust nothing by default — verify every action | Cloud environments (recommended) |
| **Hardened perimeter** | Trust inside network, block outside | Legacy on-premises, air-gapped systems |

> The cloud is generally closer to zero-trust and is more secure for most organisations because every action requires authentication.

---

## 📋 Example Security Policy

A good security policy is **simple, practical, and actionable:**

### Protecting Credentials
- ✅ Use SSO for everything — avoid passwords where possible
- ✅ Always use MFA with SSO
- ✅ Never share passwords or credentials with anyone
- ✅ Never put credentials in code — use a secrets manager
- ✅ Disable or delete old credentials immediately
- ✅ Always apply principle of least privilege
- ✅ Beware of phishing — verify ALL credential requests through a second channel

### Protecting Devices
- ✅ Use device management (MDM) for all work devices
- ✅ Enable MFA on all devices
- ✅ Enable full disk encryption
- ✅ Don't let your device out of your sight in public
- ✅ Use "do not disturb" when screen sharing or on video calls
- ✅ Share only specific windows or tabs — never your full desktop

### Software Updates
- ✅ Update browsers immediately when prompted
- ✅ Apply minor OS updates promptly
- ✅ Wait 1-2 weeks before applying major OS version releases
- ✅ Never run beta OS versions on work devices

---

## 🔒 Security for Data Engineering Specifically

| Area | What To Do |
|---|---|
| **S3 buckets** | Always set to private — never public unless specifically required |
| **Database connections** | Use VPN or SSH tunnels, never expose directly to internet |
| **API keys and tokens** | Store in secrets manager, rotate regularly |
| **IAM roles** | Scope to minimum permissions needed, review quarterly |
| **Pipeline code** | No credentials in code — environment variables or secrets manager only |
| **Data at rest** | Enable server-side encryption on all storage |
| **Data in transit** | HTTPS only — enforce TLS on all connections |
| **PII data** | Mask, hash, or tokenize at ingestion time where possible |
| **Sensitive fields** | Apply column-level security or obfuscation |
| **Access logs** | Enable and review regularly for unusual patterns |

---

## 🏥 Working with Sensitive Data (HIPAA, GDPR Relevant)

Since data engineers often work with health, financial, and personal data:

1. **Collect minimum required data** — if you don't need it, don't ingest it
2. **Anonymise or pseudonymise** PII at the earliest possible stage
3. **Implement data retention policies** — delete data that is no longer needed
4. **Support right-to-be-forgotten** requests — have processes to delete records
5. **Document data lineage** — track where sensitive data flows through the system
6. **Audit access** — log who accessed what sensitive data and when
7. **Apply broken glass processes** — emergency approval for accessing the most sensitive data

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **Principle of least privilege** | Give only the access needed for the task at hand |
| **Security theater** | Compliance checkboxes without real security commitment |
| **Active security** | Proactively researching threats and thinking through vulnerabilities |
| **Zero trust security** | Trust nothing by default — verify every action |
| **Hardened perimeter** | Trust everything inside the network, block everything outside |
| **SSO** | Single Sign-On — one login for all systems |
| **MFA** | Multi-Factor Authentication — second verification step |
| **Secrets manager** | Secure storage for credentials and API keys |
| **Encryption at rest** | Data encrypted when stored on disk |
| **Encryption in transit** | Data encrypted when moving over a network |
| **Broken glass process** | Emergency approval required to access restricted data |
| **Ransomware** | Malware that locks data and demands payment for release |
| **Shared responsibility** | Cloud secures infrastructure, you secure what you build |
| **Air gap** | Physically isolated system with no network connection |
| **MDM** | Mobile Device Management — remote control of employee devices |
| **FERPA** | Family Educational Rights and Privacy Act |
| **HIPAA** | Health Insurance Portability and Accountability Act |
| **GDPR** | General Data Protection Regulation (Europe) |
| **POPIA** | Protection of Personal Information Act (South Africa) |
| **PII** | Personally Identifiable Information |
| **Man-in-the-middle attack** | Attacker intercepts and potentially modifies data in transit |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| No credentials in code | Audit all AHRI pipelines — remove any hardcoded credentials immediately |
| Secrets manager | Implement AWS Secrets Manager for all pipeline credentials |
| S3 bucket security | Audit all S3 bucket permissions — ensure nothing is publicly accessible |
| IAM roles | Review all AWS IAM roles — remove excess permissions |
| HIPAA/POPIA | Ensure clinical data pipelines are POPIA compliant |
| Encryption | Enable server-side encryption on all S3 buckets and databases |
| Access monitoring | Set up CloudTrail and billing alerts on AWS |
| Column-level security | Implement row and column level security in analytical tables |
| Data retention | Document data retention policies for clinical datasets |
| Backup testing | Test data restoration from backups quarterly |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [OWASP — Open Web Application Security Project](https://owasp.org)
- [AWS Security Best Practices](https://docs.aws.amazon.com/security/)
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/)
- [GDPR Overview](https://gdpr.eu)
- [POPIA — South Africa](https://popia.co.za)
- [Building Secure and Reliable Systems — O'Reilly](https://sre.google/books/building-secure-reliable-systems/)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*
