# Cantrill Tech Fundamentals — Connecting IT and Software Development to Cloud

**Certification Note** · AWS foundations · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — fundamentals section |
| Tags | networking, security, infrastructure, cloud, professional-development |
| Related Work | Adrian Cantrill Tech Fundamentals; AWS SAA study path |

</details>

## Summary

Adrian Cantrill's Tech Fundamentals was largely a structured review, not my first exposure to networking or systems. My background included enterprise IT support, security operations, infrastructure tools, software development, and local Linux/container work. The value was connecting that practical experience to clearer models of how systems communicate, protect data, recover from failures, and support cloud services.

It helped me connect the software I build to the networks, security controls, and recovery plans that keep applications running.

## Key Insights

- Clarified network encapsulation and traffic flow beyond familiar IP addressing, routing, and subnetting.
- Deepened my understanding of DNS, secure communication, key pairs, hashing, and digital signatures.
- Connected banking IT security and continuity practices to formal concepts such as RPO and RTO.
- Strengthened foundations for AWS architecture decisions without confusing service familiarity with design experience.

## Engineering Reflections

### I could trace network traffic and explain security controls more clearly

I had worked with networks, VPNs, SSH, authentication, and administrative controls, but the course made it easier to trace traffic through physical links, Ethernet frames, routed packets, and transport protocols. Cryptography likewise became less of a list of tools: I better understood the distinct purposes of encryption, hashing, signatures, and key management.

The distinction between stateful and stateless security controls also became clearer. These fundamentals matter in software development because API requests, service-to-service communication, and network security all depend on behaviors that are easier to troubleshoot when the underlying protocols make sense.

### Banking IT experience helped me understand resilience and shared responsibility

Banking IT exposed me to controlled access, directory services, MFA, monitoring, backup practices, and disaster-recovery environments. The course gave that operational experience a clearer vocabulary, especially the difference between recovery point and recovery time objectives.

The material connected redundancy and disaster recovery to broader questions of resilience. It also clarified public, private, and hybrid cloud; infrastructure, platform, and software service models; and the shared-responsibility model. Those distinctions help explain who owns which operational and security responsibilities, rather than simply where a system is hosted.

### I want AWS decisions to follow application needs

My software development experience helps me reason about APIs, dependencies, deployments, and modularity without tying every application decision to AWS. SquadSync and Soccer-Subber can establish their domain logic and service contracts locally before selecting hosting, persistence, eventing, and monitoring services.

I want to use the deeper AWS coursework to evaluate those choices on security, reliability, operations, and cost—not merely to attach cloud services to a portfolio project.

## Significance

I came away better able to explain how networking, security controls, and recovery planning affect the software I build. That gives me a stronger foundation for AWS Solutions Architect Associate study and for evaluating cloud services based on actual application needs.
