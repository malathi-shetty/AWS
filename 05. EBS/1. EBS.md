## Amazon EBS (Elastic Block Store) – In-Depth Explanation

Amazon EBS is a persistent block storage service designed to be used with EC2 instances. Unlike instance store volumes (which are ephemeral), EBS volumes persist beyond instance termination (if not deleted) and support features like snapshotting, encryption, and resizing.

---

### Key Characteristics of EBS

* Block-level storage: Ideal for use cases like OS boot volumes, databases, and file systems.
* Persistent: Data remains intact across EC2 reboots or stops.
* Attachable: Can be attached to one EC2 instance at a time in a single AZ.
* Resizable: Volumes can be increased in size without stopping the instance.
* Snapshot support: Used for backup and creating new volumes.

---

## EBS Volume Types

### 1. General Purpose SSD (gp2 / gp3)

* Balanced price/performance for most workloads
* gp3 offers consistent baseline performance and is cost-effective

### 2. Provisioned IOPS SSD (io1 / io2)

* High-performance, low-latency workloads like databases (supports thousands of IOPS)

### 3. Throughput Optimized HDD (st1)

* For large, sequential workloads (e.g., big data, log processing)

### 4. Cold HDD (sc1)

* Lowest cost HDD, good for infrequent access

### 5. Magnetic (deprecated)

* Legacy, no longer recommended

---

## EC2 Root Volume

* Created from the selected AMI
* By default, stored on EBS (not ephemeral)
* Mount point: usually `/` (root)
* Can be retained after termination (if configured)
* Can be encrypted

### View root volume:

```bash
lsblk
```

Mount point will show `/` (example: `/dev/xvda1`)

### Check root volume usage:

```bash
df -Th
```

Shows mount points, disk space, and filesystem types.

---

## Create and Attach Additional (External) EBS Volume

### Step 1: Create the Volume

1. Go to **EC2 Console > Elastic Block Store > Volumes**
2. Click **Create Volume**

   * Size: e.g., 10 GiB
   * Type: gp3
   * Availability Zone: must match your EC2 instance (e.g., `ap-south-1a`)
3. Click **Create Volume**

### Step 2: Attach to EC2 Instance

1. Select the volume > **Actions > Attach Volume**
2. Choose your instance
3. Device name: e.g., `/dev/xvdf`

---

## Step-by-Step: Format and Mount EBS Volume in Linux (Ubuntu)

### 1. Connect to the instance

```bash
ssh -i my-key.pem ubuntu@<public-ip>
```

### 2. Check for the new device

```bash
lsblk
```

Output will show `/dev/xvdf` (new disk with no mount point)

### 3. Create a filesystem

```bash
sudo mkfs -t ext4 /dev/xvdf
```

### 4. Create mount point

```bash
sudo mkdir /mnt/data
```

### 5. Mount the volume

```bash
sudo mount /dev/xvdf /mnt/data
```

### 6. Check mount confirmation and usage

```bash
df -Th
```

This confirms that `/dev/xvdf` is mounted at `/mnt/data` with ext4 filesystem.

### 7. View UUID of the volume

```bash
sudo blkid /dev/xvdf
```

Example output:

```
/dev/xvdf: UUID="a1b2c3d4-e5f6-7890-abcd-1234567890ef" TYPE="ext4"
```

### 8. Add Entry in `/etc/fstab` for Persistent Mount

```bash
sudo nano /etc/fstab
```

Add the line:

```
UUID=a1b2c3d4-e5f6-7890-abcd-1234567890ef /mnt/data ext4 defaults,nofail 0 2
```

### 9. Test fstab configuration

```bash
sudo mount -a
```

If no errors occur, the configuration is safe and persistent.

---

## Resizing (Expanding) an EBS Volume

### Step 1: Modify Volume from AWS Console

1. Go to **EC2 > Volumes**
2. Select volume > **Actions > Modify Volume**
3. Increase size (e.g., from 10 GiB to 20 GiB)
4. Click **Modify**, then **Yes** to confirm

### Step 2: Resize the File System (Linux)

#### 1. View current disk size:

```bash
lsblk
```

#### 2. If using ext4, resize as:

```bash
sudo resize2fs /dev/xvdf
```

*Note*: If there's a partition like `/dev/xvdf1`, use `growpart` first:

```bash
sudo growpart /dev/xvdf 1
sudo resize2fs /dev/xvdf1
```

### 3. Confirm expansion

```bash
df -Th
```

This shows the increased available space on `/mnt/data`.

---

## Additional Useful Linux Commands

### Show disk usage of mounted directories

```bash
du -sh /mnt/data
```

### View inode usage

```bash
df -i
```

### Display kernel's current mount table

```bash
mount | column -t
```

### View all partitions and disk layout

```bash
sudo fdisk -l
```

### Create a dummy 500MB file to simulate usage

```bash
sudo dd if=/dev/zero of=/mnt/data/testfile bs=1M count=500
```

### Delete dummy file

```bash
sudo rm -f /mnt/data/testfile
```

---

## Real-World Teaching Use Case

When demonstrating EBS in class:

* Show the root volume and `/` mount
* Create a new 10GB volume, attach it, format and mount it
* Use `dd` or `touch` to create dummy files and simulate real usage
* Expand the volume, then run resize commands
* Reboot the instance to validate `/etc/fstab` works

---

Remember this:

* Persistent storage is central to any real workload
* Incorrect `/etc/fstab` configs can break booting
* Understanding EBS helps in backup, scaling, and troubleshooting
