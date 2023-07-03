# AWS_VPC_LB_ASG_Bastion


1. Goto VPC in AWS Console 


2. Click on create VPC

Go with VPC and more

Visualize what the AWS creates for you

Give a name for the project

NAT gateway - 1 per AZ

VPC endpoints - None

Click on Create VPC

Now AWS creates a bunch of resources


3. Goto EC2 and on the left pane search for Auto-Scaling groups

Auto-Scaling group depends on Launch template 


4. So click on Create a launch template

Give a name for the Launch template

Now give Templace version description - "POC for app deploy in private subnet"

Now select an AMI - Ubuntu

Instance type - Free one can be choosen as of now

Key pair name - Create new or select already created 

In Network settings - Create security group

Give name to Security gorup name

Give description to Description - "Allow SSH access"

VPC - Select the VPC which we have created in the Step 2.

Click on Add security group rule - Open ports 22,8000 from Anywhere

Now click on Create launch template


5.  Now go back to the Step3. 

Give name for the Auto Scaling group name

Launch Template - Select the template that is created in Step 4.

Click on Next

VPC - Select the VPC which we have created in the Step 2.

Availability Zones and subnets - Select all(2) private subnets as per the Block Diagram 

Click on Next

Click on Next

Group size(choose as per your needs: 
	Desired capacity - 2
	Minimum capacity - 1
	Maximum capacity - 4

Click on Next

Click on Next

Click on Next

Click on Create Auto Scaling group

Wait for sometime and verify the EC2 instances and the subnets  are created

Also verify that EC2 must not have any public IP assigned to it


6. Create a Bastion host for accessing the EC2 instances in provate subnets

Got to EC2 

Click on Launch instances

Give name to the EC2 

Select Ubuntu as the image (any image can be selected)

Choose a Instance type

Attach a keypair

In Network settings - Click on Create security group - Make sure the Allow SSH traffic from Anywhere is selected

In Network settings - CLick on Edit - Now select the VPC that we have created earlier - Select a public subnet under the VPC earlier created - Enable Auto-assign public IP

Now click on Luanch instance


7. We are using Bation to connect to the EC2 in the private subnets 

But for connecting to the EC2 we nned to have Keypair in the Bastion for which we need to copy bthe Keypair from the Local to the Bastion and then connecting to the Bastion and using the Keypair to connect to teh EC2

To copy to Bastion from the Local

scp -i /c/Users/pavan/Downloads/AWSKEYPAIR.pem /c/Users/pavan/Downloads/AWSKEYPAIR.pem ubuntu@3.145.106.67:/home/ubuntu


8. SSH to the Bastion host using the Public IP of it 

Verify the file copied


9. Now logging into the one of the EC2 in private subnet from the Bastion

sudo ssh -i AWSKEYPAIR.pem ubuntu@10.0.153.204

Now your logged into the EC2 instance


10. Create a simple Python application

vim index.html

Add the below and save it

<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>

<h1>This is Pavan - 1</h1>
<p>Testing AWS VPC</p>

</body>
</html>

python3 -m http.server 8000
(Creates a HTTP service for running the HTML)

Similarly do the Step 10. on another EC2 instance and start the HTTP service change the HTML content "This is Pavan - 2"


11. Now goto Load Balancer from AWS Console

Click on Create load balancer

Select the Application Load Balancer which is a L7 LB

Give name to Load balancer name

VPC - Select the VPC which we have created in the Step 2.

Select the both Availability Zones and make sure they are mapped to the public subnet

Security groups - Select the security groups created in Step 4. and addd HTTP 80 inbound to that, this where you define how the LB can be accessed from outside

Create target group


12. Now creating a target group

Give name to Target group name

Port - 8000

Click on Next

Now select the two EC2 instances

Now click on Include as pending below

Click on Create target group


13. Go back to Step 11. ending 

Refresh and select the target group in Default action of Listener in Listeners and routing 

Now click on Create load balancer


14. Access the ALB DNS name 