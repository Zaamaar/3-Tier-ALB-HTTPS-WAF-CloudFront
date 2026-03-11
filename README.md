# Secure & Globally Accelerated 3-Tier Web Architecture on AWS

A production-grade **3-tier web application** architecture on AWS featuring:

- Global edge delivery and caching with **CloudFront**
- Web application firewall protection at the edge (**AWS WAF**)
- TLS encryption everywhere (**ACM**)
- Internal load balancing (**ALB**)
- Private application and database tiers
- Multi-AZ high availability across all layers


*(Static SVG – zoomable directly in the browser. No editing required.)*

## Architecture Overview

- **Region**: us-east-1 (multi-AZ: us-east-1a & us-east-1b)
- **VPC**: Single VPC with public and private subnets in both Availability Zones

### Traffic Flow
1. **DNS** → Route 53 resolves custom domain to CloudFront distribution
2. **Client** → CloudFront edge location (caches content, applies WAF rules)
3. **CloudFront** → Origin (internal Application Load Balancer)
4. **ALB** → Presentation tier (Auto Scaling Group) → Application tier (Auto Scaling Group) → RDS MySQL (Multi-AZ)
5. **Outbound internet** from private subnets → NAT Gateway (in public subnets)

### Key Components

- **Global Edge Layer**
  - **CloudFront** – Low-latency caching, global distribution, DDoS protection
  - **AWS WAF** – Protects against common web exploits (SQL injection, XSS, bots)
  - **ACM** – Free TLS certificates for HTTPS

- **Public Tier**
  - Public subnets (one per AZ)
  - NAT Gateways for private outbound internet access

- **Application Tier (Private)**
  - **Auto Scaling Groups (ASG)** for presentation and application layers
  - EC2 instances in private subnets (no public IPs)
  - Automatic scaling and self-healing

- **Data Tier (Private)**
  - **Amazon RDS MySQL (Multi-AZ)**
    - Primary instance in us-east-1a
    - Standby synchronous replica in us-east-1b
    - Automatic failover with near-zero data loss

- **Observability**
  - **CloudWatch Logs** – Centralized logging

## Design Goals & Benefits

- **Security** — Edge-level WAF, private compute/database, TLS termination, restricted origins
- **Performance** — Global caching reduces latency and origin load
- **High Availability** — Multi-AZ redundancy for ALB, ASGs, and RDS
- **Scalability** — Auto Scaling Groups handle demand automatically
- **Cost Efficiency** — Caching lowers data transfer and compute costs

## Production Hardening Suggestions

- Use **Origin Access Control (OAC)** on CloudFront to restrict ALB access
- Make the ALB **internal** (no public exposure)
- Move presentation tier ASG to private subnets (or serve static content from S3 via CloudFront)
- Add scaling policies (e.g., CPU-based target tracking)
- Enable CloudWatch alarms and X-Ray for tracing
- Use IAM roles for instances (no hard-coded credentials)

Built with AWS best practices in mind.

Questions or suggestions? Feel free to open an issue!
