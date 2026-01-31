### EC2 Storage Decisions (EBS vs EFS vs Instance Store)

This repository is part of my AWS Solutions Architect Associate (SAA-C03) "build-as-I-learn" path.

The goal here is to demonstrate Architect-level storage decision-making for EC2 workloads by building and validating core storage patterns and documenting the trade-offs.

This build focuses on:
- **EBS** (persistent block storage)
- **EFS** (shared network file storage)
- **Instance Store** (ephemeral storage)
- Snapshots, restores, and AMIs (state capture and recovery)

All tasks are implemented and verified using the AWS CLI to reinforce repeatability and automation-oriented workflows.

--

#### What was built / results

- Choose the correct storage option based on **state, durability, performance, and cost**
- Understand persistence and recovery using **EBS snapshots**
- Explain **immutable infrastructure** using AMIs
- Mount and validate **EFS** and explain when it is the wrong choice

#### 1 - EBS Fundamentals
- Create and attach an EBS volume to an EC2 instance
- Format and mount the volume
- Detach and reattach (persistence validation)

#### 2 - Snapshots and Restore
- Create an EBS snapshot
- Restore a new volume from snapshot
- Validate data persistence after restore

#### 3 - AMI and Immutable Infrastructure
- Create an AMI from an EC2 instance
- Launch a new instance from the AMI
- Explain how AMIs support immutable infrastructure patterns

#### 4 - EFS Fundamentals
- Create an EFS file system
- Mount EFS to EC2
- Validate shared storage behavior
- Document common misuses and when EFS is not appropriate

#### 5 - Storage Comparison (Decision Table)
- Compare **EBS vs EFS vs Instance Store** across:
  - durability
  - performance characteristics
  - cost model
  - best-fit use cases

--

#### Repository Structure

```bash
├── cli-commands/         # Commands executed for each task
├── docs/                 # Design decisions and trade-off explanations
├── policy-examples/      # Policy artifacts (only if used by CLI)
└── evidences/            # Optional supporting artifacts (redacted if needed)
```
