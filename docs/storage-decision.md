### What EBS is (and why it matters)

Amazon Elastic Block Store (EBS) is persistent block storage that attaches to an EC2 instance within the same Availability Zone. It behaves like a virtual hard disk: the operating system can format it, mount it, and use it for filesystems, databases, and application data.

This exercise validates persistence by writing data to the mounted volume, detaching it from the instance, and reattaching it while confirming the data remains intact, making reliable EC2 architectures possible.