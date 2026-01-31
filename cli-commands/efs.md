### Task 4 - EFS Fundamentals

This task creates an EFS file system and mounts it to an EC2 instance using NFS (TLS via mount helper).

First part: Create and Mount EFS

1 - Gather required details (VPC + Subnet + SG)

To get VPC ID of your EC2 instance:
```bash
$VPC_ID = aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].VpcId" --output text

$VPC_ID
```

Get subnet ID of your EC2 instance:
```bash
$SUBNET_ID = aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].SubnetId" --output text

$SUBNET_ID
```

Get the instance security group ID
```bash
$INSTANCE_SG = aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].SecurityGroups[0].GroupId" --output text

$INSTANCE_SG
```

2 - Create an EFS file system:
```bash
$EFS_ID = aws efs create-file-system --creation-token build3-efs --encrypted --tags Key=Name,Value=build3-efs --query FileSystemId --output text

$EFS_ID
```

3 - Create an EFS mount target (same VPC/subnet)
```bash
# EFS needs a mount target in the subnet/AZ you will mount from
$MOUNT_TARGET_ID = aws efs create-mount-target --file-system-id $EFS_ID --subnet-id $SUBNET_ID --security-groups $INSTANCE_SG --query MountTargetId --output text

$MOUNT_TARGET_ID
```
Wait until lifecycle state is "available"
```bash
aws efs describe-mount-targets --file-system-id $EFS_ID
```

4 - Allow NFS traffic in Security Group (port 2049)
-> EFS uses NFS on TCP/2049.
-> Add an inbound rule allowing NFS from your instance SG (best practice is SG-to-SG).
```bash
aws ec2 authorize-security-group-ingress --group-id $INSTANCE_SG --protocol tcp --port 2049 --source-group $INSTANCE_SG

  # This allows EC2 instances using the same SG that i am using on EC2 to mount EFS.
```

5 - Mount EFS on EC2 (SSH into it)

Install EFS mount helper (Amazon Linux 2023):
```bash
sudo dnf -y install amazon-efs-utils
```
Create mount point:
```bash
sudo mkdir -p /mnt/efs
```
Mount:
```bash
sudo mount -t efs -o tls <EFS_ID>:/ /mnt/efs
```
and verify:
```bash
df -h | grep efs
mount | grep efs
```

6 - Validate persistence (simple test)
```bash
echo "EFS test $(date -u)" | sudo tee /mnt/efs/efs-proof.txt
sudo cat /mnt/efs/efs-proof.txt
```

As I'm seeing the files and correct (date) this proves EFS funcionality/connectivity and persistency! 


SECOND PART - WHEN EFS IS WRONG.. is explored at "docs/efs-when-wrong.md"