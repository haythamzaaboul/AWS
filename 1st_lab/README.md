# Week 1 – AWS Hands-On: EC2 Web, S3 Assets, IAM, VPC

## Architecture
- VPC: `10.0.0.0/16`
- Public Subnet A: `10.0.1.0/24` (EC2 web)
- Private Subnet A: `10.0.2.0/24`
- Internet Gateway for public subnet
- NAT Gateway (+ EIP) for private egress (optional if using S3 Gateway Endpoint)
- S3 bucket: `aws-week1-s3-lab` (private, versioning on)
- IAM Role on EC2: S3 read (ListBucket/GetObject) + SSM core
- (Bonus) S3 Gateway VPC Endpoint on private route table

## Steps
1. **VPC + Subnets**
   - Create VPC `10.0.0.0/16`
   - Public subnet `10.0.1.0/24`, private subnet `10.0.2.0/24` (both in the same AZ for this lab)
2. **Routes Table**
   - Public RT: `0.0.0.0/0 -> IGW`
   - Private RT: `0.0.0.0/0 -> NAT` 
   - S3 Gateway Endpoint associated to private RT
3. **Security Group**
   - Inbound: HTTP 80 from `0.0.0.0/0`, SSH 22 from my IP `/32`
   - Outbound: All traffic `0.0.0.0/0`
4. **EC2**
   - Amazon Linux 2023, t2.micro, public subnet, public IP = enabled
   - Attach IAM role with: `AmazonSSMManagedInstanceCore` + S3 read policy for the custom policy used : `AmazonSSMManagedInstanceCore_policy.json`
5. **Apache**
   ```bash
   sudo dnf update -y
   sudo dnf install -y httpd
   sudo systemctl enable --now httpd
