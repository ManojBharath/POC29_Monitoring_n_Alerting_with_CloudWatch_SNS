# Architecture Overview

EC2 / Lambda
     ↓ (metrics)
CloudWatch (CPU / Errors)
     ↓ (Alarm triggers)
CloudWatch Alarm
     ↓
SNS Topic
     ↓
Email Notification

###############################

# Step-by-Step Implementation

# Step 1: Create EC2 Instance (for CPU Monitoring)
Go to AWS Console → EC2 → Launch Instance
Select:
AMI: Amazon Linux 2 / Ubuntu or any other Linux family
Instance Type: t2.micro (Free tier)
Enable Detailed Monitoring
In "Advanced details" → Enable Detailed Monitoring (1-minute)
Launch instance with key pair

# Step 2: Install Stress Tool (Simulate High CPU)

- SSH into EC2:
ssh -i your-key.pem ec2-user@<public-ip>

- Install stress tool:
sudo yum install stress -y  
sudo apt install stress -y 

# Step 3: Create SNS Topic
Go to SNS → Topics → Create Topic
Type: Standard
Name: cpu-alert-topic
Subscribe Email
Open topic → Create subscription
Protocol: Email
Enter your email
Confirm subscription from inbox

# Step 4: Create CloudWatch Alarm (CPU Utilization)
Go to CloudWatch → Alarms → Create Alarm
Select Metric:
EC2 → Per Instance Metrics → CPUUtilization
Choose your instance
Configure Alarm
Threshold type: Static
Condition:
CPUUtilization > 70%
Period: 1 minute
Evaluation: 2 consecutive periods
Add Notification
Select SNS Topic: cpu-alert-topic
Create alarm

# Step 5: Trigger High CPU (Test)
Run this command in EC2:
stress --cpu 4 --timeout 300s

This will:
Use CPU heavily for 5 minutes
Trigger CloudWatch alarm

# Step 6: Verify Alert
You should receive:
Email like: 
ALARM: CPUUtilization > 70%
Instance: i-xxxxx
State: ALARM
