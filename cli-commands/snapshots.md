### Task 2 - EBS Snapshots and Restore

This task validates EBS snapshot usage for backup and recovery and:

- understand point-in-time backups
- know snapshots are incremental
- restore data independently of the original volume
- understand why snapshots ≠ live replication

1 - Create an EBS Snapshot:

```bash
$SNAPSHOT_ID = aws ec2 create-snapshot --volume-id <VOLUME_ID> --description "Build3 Task2 - EBS snapshot for restore test" --tag-specifications "ResourceType=snapshot,Tags=[{Key=Name,Value=build3-ebs-snapshot}]" --query SnapshotId --output text

$SNAPSHOT_ID
```

2 - Create a New Volume from the Snapshot:
```bash
$RESTORED_VOLUME_ID = aws ec2 create-volume --snapshot-id $SNAPSHOT_ID --availability-zone <SAME_AZ_AS_INSTANCE> --volume-type gp3 --tag-specifications "ResourceType=volume,Tags=[{Key=Name,Value=build3-ebs-restored}]" --query VolumeId --output text

$RESTORED_VOLUME_ID
```

3 - Detach Original Volume (clean test purpose)
```bash
aws ec2 detach-volume --volume-id <ORIGINAL_VOLUME_ID>
aws ec2 wait volume-available --volume-ids <ORIGINAL_VOLUME_ID>
```

4 - Attach the Restored Volume
```bash
aws ec2 attach-volume --volume-id $RESTORED_VOLUME_ID --instance-id <INSTANCE_ID> --device /dev/sdf

aws ec2 wait volume-in-use --volume-ids $RESTORED_VOLUME_ID
```

5 - Mount and Validate Data (SSH into EC2)
```bash
sudo lsblk

sudo mount /dev/nvme1n1 /data
sudo cat /data/ebs-proof.txt
```

When I see the previous file (ebs-proof.txt) means that the snapshot from previous (and detached) volume , is persistent and can be used as recovery approach.