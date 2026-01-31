## Architect Thinking

### What EBS is (and why it matters)

Amazon Elastic Block Store (EBS) is persistent block storage that attaches to an EC2 instance within the same Availability Zone. It behaves like a virtual hard disk: the operating system can format it, mount it, and use it for filesystems, databases, and application data.

This exercise validates persistence by writing data to the mounted volume, detaching it from the instance, and reattaching it while confirming the data remains intact, making reliable EC2 architectures possible.

--

### Task 2 - Snapshots and Recovery

EBS snapshots provide point-in-time backups that can be restored to new volumes.  
This task demonstrates recovery by restoring a volume from a snapshot and validating that previously written data is preserved.

--

### Task 3 - Immutable Infrastructure

-> Changes are made by building new images, not by modifying running servers.

Immutable infrastructure is a pattern where servers are never modified after deployment.

Instead of patching or changing running instances, a new machine image (AMI) is built and new instances are launched from it. Old instances are then terminated.

This approach reduces configuration drift, improves repeatability, and simplifies rollback by allowing teams to revert to a known-good image.

--

### Task 4 - Amazon Elastic File System (EFS) fundamentals

Amazon EFS is a managed, shared network file system provided by AWS.

-> A Linux NFS file system that many EC2 instances can mount at the same time, across multiple Availability Zones. Same as NFS on Linux, or NAS in a DC.

#### When EFS is the right choice
Typical real-world use cases:
- Web servers sharing /var/www
- Container workloads (EKS / ECS)
- Home directories
- CI/CD build artifacts
- Lift-and-shift legacy apps using NFS
- Shared logs (not high-performance logging)
- Shared content accessed by multiple EC2 instances (web assets, uploads, shared configs)
- Multi-instance compute requiring the same filesystem view
- Centralized file storage without managing NFS servers

#### When EFS Is the Wrong Choice
EFS is managed NFS storage designed for shared file access across multiple instances. It is not a default replacement for EBS.

When NOT to use EFS:
 X Ultra-low latency databases
 X High-IOPS transactional workloads
 X Windows-first environments
 X Cheap cold storage (S3 is cheaper)

##### where EFS is wrong
- **Single-instance workloads**
  If only one EC2 instance needs storage, EBS is simpler, cheaper, and lower-latency.

- **High IOPS / low-latency block storage needs**
  EFS is network-attached; workloads needing predictable low latency or high IOPS typically fit EBS better.

- **Databases**
  Most databases require block-level semantics and predictable latency; EFS often performs poorly or behaves unexpectedly for database workloads.

- **Local scratch / temporary data**
  Instance Store is often a better choice for ephemeral scratch data with high throughput requirements.

--

### Final assumptions

This lab demonstrates that storage decisions are architectural decisions.

Choosing between EBS, EFS, and Instance Store requires understanding durability, performance, and failure modes. Selecting the wrong storage type often results in unnecessary cost, poor performance, or data loss
