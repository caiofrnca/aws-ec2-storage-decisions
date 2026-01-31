### Task 3 - AMI and Immutable Infrastructure

This task demonstrates how an EC2 instance can be captured as an Amazon Machine Image (AMI) and reused to launch identical instances.

- understand that AMIs capture instance state + configuration
- know AMIs are not backups (snapshots are)
- explain immutable vs mutable infrastructure
- understand how AMIs enable repeatable deployments

1 - Prepare the Instance:

SSH into the EC2 instance and ensure it’s in a clean state:
```bash
sudo systemctl status httpd
df -h
```

Stop services to a clean image (good practice):
```bash
sudo systemctl stop httpd
```

-> You do not need to stop the instance manually; AWS will handle consistency. Exit SSH

2 - Create an AMI from the EC2 Instance:
```bash
$AMI_ID = aws ec2 create-image --instance-id <INSTANCE_ID> --name "build3-immutable-ami" --description "Build 3 - AMI for immutable infrastructure demo" --no-reboot --query ImageId --output text

# the " --no-reboot" avoids downtime for this lab, but for production, is safer for data consistency to reboot.

$AMI_ID
```

3 - Launch a New EC2 Instance from the AMI
-> proves immutability: new instance, same configuration.
```bash
$NEW_INSTANCE_ID = aws ec2 run-instances --image-id $AMI_ID --instance-type t3.micro --key-name <KEY_NAME> --associate-public-ip-address --query "Instances[0].InstanceId" --output text

$NEW_INSTANCE_ID
```

Gets its public IP:
```bash
aws ec2 describe-instances --instance-ids $NEW_INSTANCE_ID --query "Reservations[0].Instances[0].PublicIpAddress" --output text
```

4 - Validate the AMI Worked
SSH into the new instance:
```bash
ssh -i <KEY_FILE>.pem ec2-user@<NEW_PUBLIC_IP>
```
and validate:
```bash
uname -a
ls /var/www/html
```

We can acess the new EC2 instance and identity that the system looks identical and previous web servers config and persistent files from other exercices are there!