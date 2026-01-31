### Task 1 - EBS Fundamentals (Attach / Detach / Persistence)

Validates that is persistent block storage that remains intact across detach/attach operations.

1 - Identify EC2 instance and its Availability Zone (AZ):

To list instances :
```bash
aws ec2 describe-instances --query "Reservations[].Instances[].{ID:InstanceId,State:State.Name,AZ:Placement.AvailabilityZone,PublicIP:PublicIpAddress}" --output table
```
Get the instance AZ (EBS volume MUST match)
```bash
$AZ = aws ec2 describe-instances --instance-ids $INSTANCE_ID --query "Reservations[0].Instances[0].Placement.AvailabilityZone" --output text

$AZ
```

2 - Create a new EBS volume (gp3) in the same AZ

Ex. 8GiB gp3 (good free-tier baseline)
```bash
$VOLUME_ID = aws ec2 create-volume --availability-zone $AZ --size 8 --volume-type gp3 --tag-specifications "ResourceType=volume,Tags=[{Key=Name,Value=build3-ebs-data}]" --query "VolumeId" --output text

$VOLUME_ID
```

3 - Attach the volume to the EC2 instance
/dev/sdf as a standard attachment name:
```bash
aws ec2 attach-volume --volume-id $VOLUME_ID --instance-id $INSTANCE_ID --device /dev/sdf
```

4 - SSH into EC2 and format + mount

SSH in
```bash
ssh -i <KEY_FILE>.pem ec2-user@<PUBLIC_IP>
```

4.1 - Confirming that disk appears on EC2
```bash
lsblk
```
You should see a new device

4.2 - Create a filesystem (EXT4)
```bash
sudo mkfs -t ext4 /dev/nvme1n1
```
Mount it:
```bash
sudo mkdir -p /data
sudo mount /dev/nvme1n1 /data
df -h | grep /data
```
and create something to proof/identify the test:
```bash
echo "EBS persistence test $(date -u)" | sudo tee /data/ebs-proof.txt
sudo cat /data/ebs-proof.txt
```

5 - Detach the volume (persistence validation)

5.1 - Unmount cleanly first (on EC2)
```bash
sudo umount /data
```
exit SSH

5.2 - Detach using CLI (PowerShell)
```bash
aws ec2 detach-volume --volume-id $VOLUME_ID
aws ec2 wait volume-available --volume-ids $VOLUME_ID
```

6 - Reattach and verify the "old" data
```bash
aws ec2 attach-volume --volume-id $VOLUME_ID --instance-id $INSTANCE_ID --device /dev/sdf
aws ec2 wait volume-in-use --volume-ids $VOLUME_ID
```
6.2 - SSH back into EC2 and mount again
```bash
sudo mount /dev/xvdf /data
sudo cat /data/ebs-proof.txt
```
I see the message that I created when first attached (before detach). Proving that is persistent!
