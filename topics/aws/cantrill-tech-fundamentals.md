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

It also helped me articulate the bridge between implementing applications and understanding the infrastructure and operational boundaries on which they depend.

## At a Glance

- Clarified network encapsulation and traffic flow beyond familiar IP addressing, routing, and subnetting.
- Deepened my understanding of DNS, secure communication, key pairs, hashing, and digital signatures.
- Connected banking IT security and continuity practices to formal concepts such as RPO and RTO.
- Strengthened foundations for AWS architecture decisions without confusing service familiarity with design experience.

## Key Themes / Reflections

### Networking and security became a clearer end-to-end system

I had worked with networks, VPNs, SSH, authentication, and administrative controls, but the course made it easier to trace traffic through physical links, Ethernet frames, routed packets, and transport protocols. Cryptography likewise became less of a list of tools: I better understood the distinct purposes of encryption, hashing, signatures, and key management.

The value for software development is practical. API requests, service-to-service communication, and operational security all rely on behaviors that are easier to debug and protect when the underlying protocols make sense.

### Enterprise operations connected to resilience and service ownership

Banking IT exposed me to controlled access, directory services, MFA, monitoring, backup practices, and disaster-recovery environments. The course gave that operational experience a clearer vocabulary, especially the difference between recovery point and recovery time objectives.

It also introduced clearer distinctions among public, private, and hybrid cloud and among infrastructure, platform, and software service models. Those models describe ownership and management responsibility, not simply whether a system is reachable from the public internet.

### Cloud integration should follow application needs

My software development experience helps me reason about APIs, dependencies, deployments, and modularity without tying every application decision to AWS. SquadSync and Soccer-Subber can establish their domain logic and service contracts locally before selecting hosting, persistence, eventing, and monitoring services.

I want to use the deeper AWS coursework to evaluate those choices on security, reliability, operations, and cost—not merely to attach cloud services to a portfolio project.

## Why It Matters

The fundamentals section helped connect hands-on IT operations and software development into a more deliberate systems-architecture learning path. It provided a foundation for AWS Solutions Architect Associate study while reinforcing that practical architecture starts with sound networking, security, and software boundaries.
