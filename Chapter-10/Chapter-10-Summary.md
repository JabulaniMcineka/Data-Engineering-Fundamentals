# Chapter 10 - Security and Privacy

## Definition
Security is not an afterthought in data engineering — it is the first thing
to think about at every stage of the lifecycle and every aspect of the job.
A data engineer handles sensitive data, credentials, and access daily.
One breach or data leak can destroy a business, a career, and a reputation.

## Security Is a People Problem First
- The weakest link in security is always a human being
- Exercise the power of negative thinking — assume you are always a target
- Always be paranoid — confirm requests for credentials before acting
- You are the first line of defence for privacy and ethics in your organisation

## The Principle of Least Privilege
- Give users and systems ONLY the access they need to do their job
- Remove access immediately when it is no longer needed
- Applies equally to humans and service accounts and machines
- Never give administrative access unless absolutely required
- Use column, row, and cell-level access controls for sensitive data
- Broken glass process — emergency approval required to access the most sensitive data

## Three Pillars: People, Processes, Technology

### People
- Security is the weakest at the human level
- Never share passwords or credentials with anyone
- Always use SSO and multifactor authentication
- Never put credentials in code or version control — use a secrets manager
- Raise ethical concerns about sensitive data immediately

### Processes
- Security theater vs security habit — compliance checklists are not enough
- Make security a cultural habit — train regularly, review monthly
- Active security — think through real attack scenarios specific to your organisation
- Back up your data regularly and test the restoration process
- Shared responsibility in the cloud — the vendor secures the infrastructure, YOU secure what you build on top of it

### Technology
- Patch and update systems and dependencies regularly
- Encrypt data at rest — full disk encryption, server-side encryption, database encryption
- Encrypt data in transit — always use HTTPS, never FTP over public internet
- Monitor access, resource usage, billing, and excess permissions
- Set up automated alerting for unusual patterns
- Never open network access broadly — restrict to specific IPs and ports
- Zero trust security — every action requires authentication (cloud model)
- Hardened perimeter security — trust everything inside, block everything outside (legacy model)

## Key Security Practices

| Practice | What To Do |
|---|---|
| Credentials | Use SSO, MFA, secrets manager — never in code |
| Devices | Full disk encryption, device management, MFA |
| Access control | Principle of least privilege — remove when done |
| Encryption | At rest and in transit — always |
| Monitoring | Access logs, resource usage, billing, permissions |
| Backup | Regular backups — test restoration process |
| Network | Restrict IPs and ports — no open public access |
| Updates | Patch regularly — automate where possible |

## Key Privacy Regulations to Know
- FERPA - Family Educational Rights and Privacy Act (USA, education)
- HIPAA - Health Insurance Portability and Accountability Act (USA, health)
- GDPR - General Data Protection Regulation (Europe)
- CCPA - California Consumer Privacy Act (USA, California)
- Penalties for violations can be devastating to a business

## Key Insight
The best way to protect sensitive data is to not collect it in the first place.
If you do not need the data downstream, do not ingest it.
Data engineers should actively think through attack and leak scenarios
for every pipeline and storage system they build or maintain.

## Key Terms I Learned
- Principle of least privilege - give only the access needed for the task at hand
- Security theater - compliance checkboxes without real security commitment
- Active security - proactively researching threats and thinking through vulnerabilities
- Zero trust security - trust nothing by default, verify every action
- Hardened perimeter security - trust inside network, block everything outside
- SSO - Single Sign-On, one login for all systems
- MFA - Multi-Factor Authentication, second verification step
- Secrets manager - secure storage for credentials and API keys
- Encryption at rest - data is encrypted when stored on disk
- Encryption in transit - data is encrypted when moving over a network
- Broken glass process - emergency approval required to access restricted data
- Ransomware - malware that locks data and demands payment
- Shared responsibility model - cloud secures infrastructure, you secure what you build
- Air gap - physically isolated system with no network connection

## What This Means for Me
At AHRI I work with clinical and health research data — highly sensitive.
I am subject to HIPAA-equivalent regulations in South Africa.
I need to:
- Audit all credentials and remove any hardcoded passwords from code
- Implement a secrets manager for all pipeline credentials
- Review all S3 bucket permissions to ensure nothing is publicly accessible
- Set up access monitoring and billing alerts on AWS
- Implement column and row level security in data warehouse tables
- Document and test the data backup and restoration process
- Review and apply the principle of least privilege across all IAM roles
At Entelect security awareness will be expected as part of good data engineering practice.
