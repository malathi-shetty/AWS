## AWS Network Load Balancer (NLB) – Practical Setup and Comparison

## What is a Network Load Balancer?

* **NLB** operates at **Layer 4 (Transport Layer)** and routes TCP (and optionally UDP) traffic to targets such as EC2 instances, IP addresses, or Lambda functions.
* Designed to handle **millions of requests per second** with **very low latency**.
* Ideal for **real-time applications**, gaming, streaming, or low-latency microservices.

---

## NLB vs ALB – Key Differences

| Feature                  | Application LB (ALB) | Network LB (NLB)            |
| ------------------------ | -------------------- | --------------------------- |
| OSI Layer                | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP)           |
| Protocol Awareness       | HTTP, HTTPS          | TCP, UDP                    |
| Target Group Healthcheck | HTTP/HTTPS           | TCP                         |
| Sticky Sessions          | Supported            | Not directly supported      |
| Static IP Support        | No                   | Yes (supports Elastic IP)   |
| TLS Offloading           | Yes                  | Yes                         |
| Use Case Examples        | Web apps, APIs       | Gaming, VPN, real-time apps |

---

## Step-by-Step: Launch Two EC2 Instances for NLB

### Step 1: Launch Web Servers (Amazon Linux 2)

1. Launch 2 EC2 instances in the same VPC but different subnets
2. Security Group: Allow **TCP port 80** inbound (for HTTP)
3. SSH into each instance and install Apache:

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

4. Customize `index.html` to differentiate:

```bash
echo "<h1>Web Server A - via NLB</h1>" | sudo tee /var/www/html/index.html
```

Repeat on second instance:

```bash
echo "<h1>Web Server B - via NLB</h1>" | sudo tee /var/www/html/index.html
```

---

## Step 2: Create Target Group for NLB

1. Go to **EC2 → Target Groups → Create Target Group**
2. Choose:

   * Target type: **Instance**
   * Protocol: **TCP**, Port: **80**
   * VPC: same as EC2s
3. Health Check:

   * Protocol: **TCP**
   * Port: **traffic port** (default)
4. Register targets:

   * Choose both EC2 instances
   * Click "Include as pending"
5. Create the target group

---

## Step 3: Create the Network Load Balancer

1. Go to **EC2 → Load Balancers → Create Load Balancer**
2. Choose **Network Load Balancer**
3. Configure:

   * Name: `my-nlb`
   * Scheme: **Internet-facing** (for public access)
   * IP address type: **IPv4**
4. Listeners:

   * Protocol: **TCP**, Port: **80**
   * Forward to: previously created Target Group
5. Network Mapping:

   * Choose **at least 2 subnets** (same as EC2 AZs)
6. Optionally, allocate Elastic IPs (EIP) for static IP support
7. Click **Create Load Balancer**

---

## Step 4: Test the Load Balancer

1. Wait until both targets are healthy
2. Copy the **DNS name** of the NLB
3. Access from browser:

```
http://<NLB-DNS-name>
```

4. You will be served either Web Server A or B randomly

---

## Additional Notes

* NLB uses **TCP health checks**, so it's protocol-agnostic
* Use **Elastic IPs** for static endpoint exposure
* NLB is suitable for apps requiring high throughput and consistent low latency
* Unlike ALB, NLB **does not support path-based or host-based routing**
* For internal applications, choose **internal scheme** instead of internet-facing

