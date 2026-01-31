### EC2 Storage Comparison: EBS vs EFS vs Instance Store

This document compares EC2 storage options based on durability, performance, cost, and use cases to guide architectural decisions.

```bash
| Feature / Storage Type | EBS           | EFS                       | Instance Store          |
|------------------------|---------------|---------------------------|-------------------------|
| Storage type           | Block storage | Network file system (NFS) | Local ephemeral storage |
| Persistence            | Persistent    | Persistent                | Ephemeral               |
| Survives instance stop/terminate | Yes | Yes                       | No                      |
| Availability scope     | Single AZ     | Regional (Multi-AZ)       | Single instance         |
| Performance characteristics | Low latency, predictable | Higher latency, shared | Very high throughput |
| Scaling model | Per-volume | Automatic | Fixed per instance |
| Backup mechanism | Snapshots | AWS-managed replication | None |
| Typical cost model | Per GB + IOPS | Per GB stored | Included with instance |
| Best use cases | Databases, root volumes, single-instance apps | Shared content, multi-instance access | Scratch space, caches, temp data |
| Poor fit for | Shared multi-instance files | Low-latency databases | Any persistent data |
```
--

## Decision summary

- **EBS** is the default choice for persistent storage attached to a single EC2 instance.
- **EFS** is suitable when multiple instances require shared filesystem access.
- **Instance Store** should only be used for temporary data that can be safely lost.

Storage selection should be driven by **state requirements**, **failure tolerance**, and **access patterns**, not by service popularity.

AWS official doc: https://docs.aws.amazon.com/decision-guides/latest/storage-on-aws-how-to-choose/choosing-aws-storage-service.html
