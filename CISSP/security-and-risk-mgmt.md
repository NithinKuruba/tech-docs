# The Security and Risk Management

## Security 101

- Security is business management tool that ensures the reliable and protected operation of IT/IS
- Security exists to support the objectives, mission, and goals of the organization
- Security framework should be adopted and it needs to be fine tuned through evaluation
- Three common types of evaluation
  - `Risk assessment`: Its a process of identifying assets, threat and vulnerabilities and then using that information to calculate risk
  - `Vulnerability assessment`: Using automated tools to locate security weaknesses
  - `Penetration testing`: Stress test the security infrastructure
- Its important to select the security framework that provides greatest protection at very low cost
- Confidentiality, Integrity and Availability are the three primary goals and objectives of a security infrastructure
- Security controls are assessed on how well they address these three core information security tenets
- Vulnerabilities and risks are evaluated based on threat they pose against one or more of these triad principles

### Confidentiality

- Its the concept of the measures used to ensure the protection of the secrecy of data, objects, or resources
- The goal of the confidentiality protection is to prevent or minimize unauthorized access to data, i.e. preventing disclosure while protecting authorized access
- Counter measures include encryption, network traffic padding (adding additional data in network traffic making it more difficult to identify the sender), strict access control, rigorous authentication procedures, data classification, and extensive personnel training
- Sensitivity, Discretion, Criticality, Concealment, Secrecy, Privacy, Seclusion and Isolation

### Integrity

- Its the concept of protecting the reliability and correctness of data
- Integrity protection goal is to protect the data from unauthorized modifications or alterations
- Some countermeasures that protect Integrity are strict authentication procedures, strict access controls, encryption, hash verifications, input check, interface restrictions and personnel training
- Confidentiality and Integrity depend on each other. If integrity is lost then confidentialy cannot be maintained
- Validity, Truthfulness, Accuracy, Completeness, Comprehensiveness, Responsibility and Accountability

### Availability

- It states authorized subjects are granted timely and uninterrupted access to objects
- Its objective is to promote uninterrupted access to authorized users and prevention of Dos (Denial of service) attacks
- Controls setup to maintain availability must ensure acceptable level of performance, handle interruptions, provide for redundancy, maintain reliable backups, and prevent data loss or destruction
- Some counter measures to ensure availability are designing intermediary delivery systems, using access controls, monitoring performance and network traffic, using firewalls and routes to prevent Dos, implementing redundancy, and maintaining and testing backup systems
- Most security policies and business continuity planning (BCP), focus on the use of fault tolerance features at various levels of access/storage/security with the goal of eliminating single points of failure to maintain availability of critical systems
- Availability depends on both integrity and confidentiality
- Usability, Accessibility and Timeliness

### DAD, Overprotection, Authenticity, Non-repudiation and AAA Services

- `DAD` stands for Disclosure, Alteration and Destruction and its opposite of CIA traid
- `Overprotection` states that too much confidentiality limits availability, too much integrity can also limit availability, and overproviding availability can lead to loss of confidentiality and integrity
- `Authenticity` refers to data being genuine from source, which is related to integrity
- `Nonrepudiation` refers to subject of activity cannot deny the event occurred
  - Digital Certificates, Session Identifiers, Transaction logs, and other transactional and access control mechanisms
- `AAA Services` abbreviation is authentication, authorization and accounting/auditing 
  - It actually referes to `Identification`, `Authentication`, `Authorization`, `Auditing` and `Accounting`