# 3-Tier-ALB-HTTPS-WAF-CloudFront
# Secure & Globally Accelerated 3-Tier Web Architecture on AWS

A modern, production-grade **3-tier web application** architecture on AWS with global edge delivery, strong security, high availability, and automatic scaling.

This design features:
- Global caching & protection at the edge (CloudFront + WAF)
- TLS everywhere (ACM)
- Internal load balancing
- Private compute & database tiers
- Multi-AZ redundancy across the board

![Secure 3-Tier AWS Architecture with CloudFront & WAF](secure-3-tier-cloudfront-waf.drawio.svg)

*(Vector SVG – zoomable on GitHub. Fully editable in diagrams.net/draw.io: File → Open → GitHub → select this repo/file.)*

## Architecture Overview

- **Region**: us-east-1 (multi-AZ: us-east-1a & us-east-1b)
- **VPC**: Single VPC with public and private subnets in both AZs
- **Entry Point**: Amazon CloudFront (CDN) + AWS WAF for edge caching, DDoS protection, and web exploit blocking

### Traffic Flow
1. **DNS** → Route 53 resolves custom domain to CloudFront distribution
2. **Client** → CloudFront edge location (caches responses, applies WAF rules)
3. **CloudFront** → Origin (Application Load Balancer) via Origin Access Control (OAC) for security
4. **ALB** (internal) → Presentation tier ASG → Application tier ASG → RDS (Multi-AZ MySQL)
5. **Outbound** (from private subnets) → NAT Gateway (in public subnets) → Internet

### Key Components

- **Global Edge Layer**
  - **CloudFront** — Caches static/dynamic content, reduces latency
  - **AWS WAF** — Attached to CloudFront for OWASP Top 10 protection
  - **ACM** — Free TLS certificates for HTTPS (CloudFront + ALB)

- **Public Tier**
  - Public subnets (one per AZ)
  - NAT Gateways (for private outbound internet)

- **Application Tier (Private)**
  - **Auto Scaling Groups (ASG)** for presentation and application layers
  - Instances in private subnets (no public IPs)
  - Horizontal scaling based on demand

- **Data Tier (Private)**
  - **Amazon RDS MySQL (Multi-AZ)**
    - Primary database in us-east-1a
    - Standby (synchronous replica) in us-east-1b
    - Automatic failover (< 2 minutes, near-zero data loss)
  - DB subnet group spans both private subnets

- **Observability**
  - **CloudWatch Logs** — Centralized logging

## Design Goals & Benefits

- **Security** — WAF at edge, private compute/DB, TLS termination, OAC-restricted origins
- **Performance** — Global edge caching (CloudFront), low-latency ALB routing
- **High Availability** — Multi-AZ across ALB, ASGs, RDS, and NAT
- **Scalability** — Auto Scaling Groups handle traffic spikes automatically
- **Cost Efficiency** — Caching reduces origin load; scale down when quiet

## Production Recommendations

- Add **Origin Access Control (OAC)** on CloudFront → restrict ALB to CloudFront only
- Make ALB **internal** (no public IPs)
- Move presentation tier ASG to private subnets (or replace with S3 origin for static files)
- Add scaling policies (e.g., target tracking on CPU 50%)
- Enable **CloudWatch alarms**, **X-Ray tracing**, and detailed monitoring
- Use **IAM roles** for instances (no access keys)
- Consider **RDS Proxy** for connection pooling

## How to View / Edit the Diagram

1. Click the SVG preview above (or open the file in this repo).
2. To edit: https://app.diagrams.net → **File → Open → GitHub** → select this repository/file.

Built with diagrams.net (draw.io) + AWS icons.  
Questions, improvements, or forks welcome — open an issue or PR!

#AWS #CloudArchitecture #DevOps #WebDevelopment
