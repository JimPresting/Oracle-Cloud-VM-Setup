# 🚀 Oracle Cloud VM Setup Guide

Quick guide for setting up a VM instance on Oracle Cloud for self-hosting applications like N8N, Supabase, Chatwoot, or other open-source tools.

**Key Focus:** The most critical steps are configuring firewall rules (ingress/egress) to allow traffic to reach your applications, and securing a static public IP so you can point your domain's DNS records to a fixed address.

## 💳 Account Setup (Recommended)

To increase chances of successful VM provisioning, upgrade to **Pay-as-you-go** account:
- Navigate to **Billing & Cost Management → Upgrade and Manage Payment**
- This maintains free tier eligibility while improving resource allocation success

![Screenshot 2025-06-11 141824](https://github.com/user-attachments/assets/85fb4595-7016-48da-8248-3ce60f948a5b)

## 🖥️ VM Instance Creation

![armScreenshot 2025-06-11 140741](https://github.com/user-attachments/assets/01bbc56d-cfe3-49c6-ab7c-99e10760bf93)

**Free Tier Limits:**
- **ARM A1 Flex**: Up to 4 vCPUs and 24 GB RAM (maximum free tier allocation)
- **AMD/Intel**: VM.Standard.E2.1.Micro (1 vCPU, 1 GB RAM)

**Basic Setup:**
1. Create VM instance with your preferred OS
2. Generate SSH key pair and **securely store private key** - losing this key means losing VM access
3. Choose appropriate shape based on your needs

## 🌐 Public IP Configuration

A static public IP is essential for hosting applications as it allows you to point your domain's DNS records to a fixed address.

### Method 1: Assign During VM Creation
- ✅ **Enable "Automatically assign public IPv4 address"** during VM setup
- IP becomes permanent and survives VM restarts
- **Recommended approach**

![Scr222eenshot 2025-06-11 140429](https://github.com/user-attachments/assets/635fe3ca-a628-4922-bdd4-18174d1c6988)

### Method 2: Reserve and Assign Later
- Create VM without public IP
- Navigate to **Networking → Reserved Public IPs**
- Create reserved IP address
- Manually assign to VM's private IP later

## 🔒 Security Rules (Firewall)

**Critical Step:** Configure firewall rules to allow traffic to your applications.

### Accessing Security Rules
1. **Compute → Instances → Your VM**
2. Click on **subnet link** under networking details
3. Navigate to **Security Rules** tab
4. Click **Add Ingress Rules**

### Essential Ingress Rules
Add these rules with **Source CIDR: `0.0.0.0/0`** and **IP Protocol: TCP**:

| Port | Service | Description |
|------|---------|-------------|
| 22 | SSH | Remote access (usually pre-configured) |
| 80 | HTTP | Web traffic |
| 443 | HTTPS | Secure web traffic |
| 3000 | Applications | Custom applications (e.g., Chatwoot) |
| 5432 | PostgreSQL | Database access (if needed) |
| 6379 | Redis | Cache access (if needed) |

### Rule Configuration
- **Stateless:** Leave unchecked (use stateful rules)
- **Source Type:** CIDR
- **Source CIDR:** `0.0.0.0/0` (allows access from anywhere)
- **Destination Port Range:** Specific port number

### Ingress vs Egress
- **Ingress:** Incoming traffic TO your VM (what you need to configure)
- **Egress:** Outgoing traffic FROM your VM (usually allowed by default)

## 🌍 DNS Configuration

Once you have your static public IP, point your domain to it:

### Adding A-Record to Domain Provider
1. Login to your domain registrar (e.g., Namecheap, GoDaddy, Cloudflare)
2. Navigate to DNS management/DNS settings
3. Add new **A Record**:
   - **Type:** A
   - **Host/Name:** @ (for root domain) or subdomain (e.g., app, chat)
   - **Value/Points to:** Your Oracle Cloud static IP
   - **TTL:** 14400 (or default)

![Screenshot 2025-06-11 142336](https://github.com/user-attachments/assets/dbf67c4a-dd29-44cc-ac87-0bc3761490d4)

**Example:**
- **Host:** chat
- **Value:** 130.162.214.12
- **Result:** chat.yourdomain.com points to your VM

*Screenshots showing DNS configuration will be provided below.*

## 🔧 Connecting to Your VM

Once your VM is running and you have the public IP address, connect via SSH:

```bash
ssh -i ~/path/to/your-private-key.key ubuntu@YOUR_PUBLIC_IP
```

**Example:**
```bash
ssh -i ~/Downloads/ssh-key-2025-06-11.key ubuntu@130.162.214.12
```

## 📦 Initial VM Setup

After connecting to your VM, run these essential commands:

```bash
# Update package repository
sudo apt update

# Install network tools (for netstat and networking diagnostics)
sudo apt install net-tools


```

Your VM is now ready for application installation!

This setup provides a solid foundation for hosting applications on Oracle Cloud's free tier.
