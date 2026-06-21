# SOC 2 and ISO 27001: Roles and Responsibilities

> A short reminder for engineering teams preparing for a SOC 2 examination or ISO/IEC 27001 certification.

## Core principle

DevOps implements and operates many technical controls, but DevOps does not make the company compliant alone.

The company needs:

* Executive ownership
* Defined control owners
* Risk management
* Policies and processes
* Technical controls
* Repeatable evidence
* Independent assessment

## Small company example

Example company:

* CTO
* DevOps or Platform team
* Engineering team
* HR or Operations
* External compliance adviser
* External auditor

### CTO

The CTO should:

* Act as executive sponsor
* Approve the scope and security objectives
* Assign control owners
* Approve policies
* Accept or reject identified risks
* Resolve ownership and resource problems
* Participate in management reviews
* Ensure compliance work is not left entirely to DevOps

### DevOps / Platform team

The DevOps team should own or support:

* Cloud and infrastructure security
* Infrastructure as Code
* Identity and privileged access controls
* CI/CD security and change traceability
* Logging, monitoring and alerting
* Backup and recovery
* Vulnerability and patch management
* Secrets management
* Production access records
* Technical audit evidence

DevOps should document how controls work and retain evidence that they operate consistently.

### Engineering team

Engineering should own:

* Application security
* Code review
* Secure development practices
* Dependency remediation
* Application-level logging
* Data validation and protection
* Fixing vulnerabilities in application code
* Participating in incident response

### HR / Operations

HR or Operations should own:

* Employee onboarding and offboarding
* Background checks where applicable
* Security awareness training
* Policy acknowledgements
* Employee and contractor records
* Disciplinary and termination processes

### CTO, Operations or Legal

Someone must also own:

* Vendor management
* Contracts and data-processing agreements
* Privacy requirements
* Data retention
* Regulatory obligations
* Business continuity planning

This may be the CTO or Operations lead in a small company, but the ownership must be explicit.

### External compliance adviser

An adviser can help with:

* Gap assessment
* Control mapping
* Policy templates
* Risk assessment
* Statement of Applicability
* Audit readiness

The adviser should guide the company, not become the permanent owner of its controls.

### External auditor

The external auditor or certification body independently assesses whether the controls and management system meet the required criteria.

They should not design or operate the controls they later audit.

## Larger company example

A larger organization will normally divide responsibilities across several teams.

### Executive sponsor

Usually the CTO, CIO or CISO.

Responsible for:

* Executive support
* Budget and staffing
* Risk acceptance
* Management review
* Resolving cross-team issues

### Security or GRC team

Responsible for:

* Compliance program coordination
* Control framework
* Risk register
* Policies
* Control ownership tracking
* Evidence coordination
* Audit preparation
* Remediation tracking

GRC coordinates the program but should not claim ownership of every technical control.

### Security Engineering

Responsible for:

* Security architecture
* Detection engineering
* Vulnerability management
* Security tooling
* Threat modelling
* Incident-response support
* Cloud and application security guidance

### DevOps / Platform / SRE

Responsible for:

* Infrastructure controls
* Deployment and change controls
* IAM implementation
* Monitoring and logging
* Backup and recovery
* Infrastructure vulnerabilities
* Production access
* Technical evidence

### Application Engineering

Responsible for:

* Secure application development
* Code review
* Application vulnerabilities
* Software dependencies
* Application data handling
* Application-level incident remediation

### IT and Identity team

Responsible for:

* Workforce identities
* SSO and MFA
* Endpoint management
* Device inventory
* Access provisioning
* Joiner, mover and leaver processes

### HR

Responsible for:

* Employment controls
* Training
* Background checks
* Policy acknowledgement
* Personnel changes

### Legal, Privacy and Procurement

Responsible for:

* Contracts
* Privacy obligations
* Vendor agreements
* Regulatory requirements
* Vendor due diligence
* Data-processing terms

### Business Continuity owner

Responsible for:

* Business impact analysis
* Continuity plans
* Recovery objectives
* Continuity exercises
* Coordination with technical disaster recovery

### Internal Audit or independent reviewer

Responsible for independently checking whether:

* Controls are correctly designed
* Controls operate consistently
* Evidence is sufficient
* Problems are tracked and corrected

The reviewer should not audit controls they personally operate.

## DevOps responsibility boundaries

DevOps should not be expected to:

* Approve company-wide risk alone
* Write every organizational policy
* Own HR processes
* Interpret contracts or privacy law
* Perform its own independent audit
* Guarantee certification
* Accept undocumented business risk
* Manufacture evidence shortly before an audit

DevOps should report missing controls, unclear ownership and unacceptable technical risks to the CTO or compliance owner.

## Recommended ownership model

Every control should have:

* **Executive owner** — accountable for the outcome
* **Control owner** — responsible for the process
* **Operator** — performs the control
* **Evidence owner** — retains proof
* **Reviewer** — checks that the control works

One person may hold several roles in a small company, but ownership should still be documented.

## Practical sequence

1. Appoint an executive sponsor.
2. Define the audit and certification scope.
3. Assign a compliance or ISMS coordinator.
4. Perform a gap and risk assessment.
5. Assign control owners.
6. Implement missing organizational and technical controls.
7. Operate controls and collect evidence.
8. Perform an internal readiness review.
9. Remediate findings.
10. Engage the external auditor or certification body.

## Final reminder

SOC 2 and ISO 27001 are company programs supported by technology.

DevOps is usually a major technical contributor, but management remains responsible for governance, resources, risk decisions and organization-wide operation of the controls.
