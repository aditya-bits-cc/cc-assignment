
# Step by Step auto scaling procedure

### 1. VPC, Subnet & Security Groups 

Run below script with three parameters VPC Name, default subnet and security group. This script is a wrapper around aws cli and creates the necessary infrastructure

```
~scripts/create-vpc-structure.sh <VPC-Name> <Subnet-Name> <SecurityGroup-Name>
```

### 2. Auto Scaling Template

This script is provided as input to the EC2 `userdata` during the launch of the Auto Scaling group. It sets up and installs the required services, and pulls the scaling web application from the GitHub repository to prepare the instance for handling incoming traffic.

script name : `auto-scaling-template.sh`

### 3. Setup Auto Scaling Groups, Launch Template, Target Groups, Network Load Balancer, Listener, Scaling Policy, Cloudwatch Alarm

```
~scripts/create-asg-nlb.sh 
```

- Auto Scaling Groups has been defined having min and max 1, 3 respectively
- Launch Template defines the default behavior of VM during scale up
- CPU threshold has been set to 70% on an avg of all VMs in the scaling group in the Cloudwatch to trigger the scaling.
- A Network Load Balancer has been created, with its target group linked to the Auto Scaling Groups.
- `high-cpu-alarm`, `low-cpu-alarm` have been created to continuous monitoring and auto trigger the scale up and down scenarios.

### 4. Static content storage in s3 bucket

- S3 bucket is used to store all the static content like videos, logs etc., in this usecase.

### 5. Simulate the stress in the existing VM to trigger high-cpu alarm

Use this script `~/scripts/stress-vm-to-scale.sh` to simulate the high CPU usage using the stress utility in the ubuntu machine that will automatically trigger the autoscaling.