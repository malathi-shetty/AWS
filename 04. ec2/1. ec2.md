## 1. Introduction to Amazon EC2 (Elastic Compute Cloud)

### What is EC2?

Amazon EC2 (Elastic Compute Cloud) is a web service provided by AWS that allows users to launch and manage virtual servers (known as EC2 instances) on demand. EC2 provides scalable computing capacity in the AWS cloud and eliminates the need to invest in hardware upfront. This enables users to develop and deploy applications faster and more cost-effectively.

### Core Features

* **Elasticity**: Automatically scale up and down according to your needs.
* **Customizable**: Choose your operating system, instance type, storage options, and networking configurations.
* **Integrated with AWS ecosystem**: EC2 can be combined with services like EBS, VPC, ELB, IAM, CloudWatch, and more.
* **Secure**: Security Groups, VPC, IAM Roles, encrypted volumes, and key pairs provide multi-layered security.

### Use Cases

* Web hosting and application servers
* Development and testing environments
* High-performance computing (HPC)
* Batch processing
* Machine learning workloads
* Media transcoding
* Big data analytics

### Example Real-World Scenario

A startup wants to deploy a dynamic web application. Using EC2, they can:

* Deploy the application on Amazon Linux instances
* Place it behind an Elastic Load Balancer
* Automatically scale the number of instances using Auto Scaling
* Use CloudWatch to monitor system health
* Store logs and backups on S3

---

## 2. EC2 Instance Types – Deep Dive

AWS offers a wide variety of instance types grouped into families based on their target use case:

### Instance Families:

1. **General Purpose (t3, t2, m5, m6)**

   * Balanced compute, memory, and networking resources.
   * Suitable for small/medium databases, development environments, and web servers.

2. **Compute Optimized (c5, c6)**

   * High-performance processors for compute-intensive workloads.
   * Ideal for gaming servers, scientific modeling, batch processing.

3. **Memory Optimized (r5, x1e, u6)**

   * High RAM for in-memory databases and analytics.
   * Ideal for SAP HANA, Redis, real-time analytics.

4. **Storage Optimized (i3, d2, h1)**

   * High IOPS and low-latency storage.
   * Ideal for large transactional databases and log processing.

5. **Accelerated Computing (p3, g4, f1)**

   * GPU and FPGA-based instances for ML, AI, and video processing.

### Instance Size Nomenclature

Example: `t3.micro`

* `t3`: Instance family (General Purpose)
* `micro`: Size of the instance (smallest)

### Comparison Table

| Instance Family     | Use Case                | Example Types           |
| ------------------- | ----------------------- | ----------------------- |
| General Purpose     | Web apps, dev servers   | t3.micro, m5.large      |
| Compute Optimized   | Game servers, CPU apps  | c5.large, c6g.medium    |
| Memory Optimized    | In-memory DB, analytics | r5.large, x1e.32xlarge  |
| Storage Optimized   | NoSQL, logging, OLTP    | i3.large, d2.8xlarge    |
| Accelerated Compute | AI, ML, graphics        | p3.2xlarge, g4dn.xlarge |

---

## 3. EC2 Pricing Models – Detailed

### 1. On-Demand Instances

* Pay per second or hour with no long-term commitment.
* Use case: Development, testing, or short-term workloads.

### 2. Reserved Instances (RIs)

* Commit to 1 or 3 years.
* Save up to 75% over On-Demand.
* Use case: Long-running applications with steady usage.

### 3. Spot Instances

* Bid for unused capacity.
* Can be interrupted by AWS with 2-minute notice.
* Use case: Batch jobs, stateless services, fault-tolerant apps.

### 4. Dedicated Hosts / Instances

* Dedicated physical servers for your use.
* Use case: Licensing compliance, strict security requirements.

### 5. Savings Plans

* Commit to a consistent usage over 1 or 3 years.
* Flexible across instance families, regions, and OS.
* Use case: Flexible savings compared to Reserved Instances.

---

## 4. Key EC2 Components

### Amazon Machine Image (AMI)

* A template for the root volume of your instance.
* Includes OS, software, and configuration.
* Types: Amazon Linux 2, Ubuntu, RHEL, Windows Server, custom AMIs.

### Instance Type

* Defines the hardware (vCPUs, RAM, network bandwidth).
* Choose based on workload needs.

### Key Pair

* SSH key pair for Linux or RDP password decryption for Windows.
* Ensure `.pem` or `.ppk` files are stored securely.

### Networking

* VPC: Virtual private network environment.
* Subnet: Divides VPC into logical groups.
* Security Group: Acts as a firewall for instances.
* Elastic IP: Static public IP address.

### Storage

* EBS: Persistent block storage.
* Instance Store: Temporary storage tied to instance lifecycle.
* Snapshot: Backup of EBS volumes.

---

## 5. Launching EC2 Instances – Full Workflow

### Launching a Linux EC2 Instance

1. Open EC2 Dashboard → Click “Launch Instance”
2. Choose AMI (Amazon Linux 2 or Ubuntu)
3. Select instance type (e.g., t2.micro)
4. Configure instance details (VPC, subnet, auto-assign public IP)
5. Add storage (e.g., 8 GB gp2 EBS)
6. Add tags (e.g., Name = WebServer01)
7. Configure Security Group (allow SSH on port 22)
8. Choose or create a key pair
9. Launch instance
10. Connect using:

```bash
chmod 400 my-key.pem
ssh -i my-key.pem ec2-user@<Public-IP>
```

### Launching a Windows EC2 Instance

1. Choose Windows Server AMI (e.g., 2019)
2. Select instance type
3. Configure networking and public IP
4. Configure security group (allow RDP on port 3389)
5. Add tags and storage
6. Create or use existing key pair
7. Launch instance
8. After launch, connect via Remote Desktop:

   * Download RDP file from EC2 console
   * Decrypt password using key pair
   * Login using "Administrator" and decrypted password
