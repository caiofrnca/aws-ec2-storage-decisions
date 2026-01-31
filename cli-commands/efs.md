### Task 4 - EFS Fundamentals

This task creates an EFS file system and mounts it to an EC2 instance using NFS (TLS via mount helper).

First part: Create and Mount EFS

1 - Gather required details (VPC + Subnet + SG)

To get VPC ID of your EC2 instance:
$VPC_ID = aws ec2 describe-instances `
  --instance-ids <INSTANCE_ID> `
  --query "Reservations[0].Instances[0].VpcId" `
  --output text

$VPC_ID
