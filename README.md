# Deploying_VPC 
DEPLOYING WORDPRESS WITH MYSQL DATABASE IN OUR OWN VPC
PROBLEM STATEMENT
1) Write a Infrastructure as code using terraform, which automatically create a VPC.

2) In that VPC we have to create 2 subnets:

  a) public subnet [ Accessible for Public World! ] 

  b) private subnet [ Restricted for Public World! ]

3) Create a public facing internet gateway for connect our VPC/Network to the internet world and attach this gateway to our VPC.

4) Create a routing table for Internet gateway so that instance can connect to outside world, update and associate it with public subnet.

5) Launch an ec2 instance which has Wordpress setup already having the security group allowing port 80 so that our client can connect to our wordpress site.

Also attach the key to instance for further login into it.

6) Launch an ec2 instance which has MYSQL setup already with security group allowing port 3306 in private subnet so that our wordpress VM can connect with the same.

Also attach the key with the same.

Note: Wordpress instance has to be part of public subnet so that our client can connect our site. Mysql instance has to be part of private subnet so that outside world can't connect to it.
Lets's Start
Step-1
* AMAZON VPC
Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network that you've defined. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS.

The following are the key concepts for VPCs:

Virtual private cloud (VPC) — A virtual network dedicated to your AWS account.
Subnet — A range of IP addresses in your VPC.
Route table — A set of rules, called routes, that are used to determine where network traffic is directed.
Internet gateway — A gateway that you attach to your VPC to enable communication between resources in your VPC and the internet.
So first we will configure aws provider and then proceed towards next part.

# Configure The Provider
The Amazon Web Services (AWS) provider is used to interact with the many resources supported by AWS. The provider needs to be configured with the proper credentials before it can be used.
# Creating VPC
Step-2
* Subnet
When you create a subnet, you specify the CIDR block for the subnet, which is a subset of the VPC CIDR block. Each subnet must reside entirely within one Availability Zone and cannot span zones. Availability Zones are distinct locations that are engineered to be isolated from failures in other Availability Zones. Here we will create 2 subnet 'Public' and 'Private' , in Public subnet we will launch wordpress instance and attach internet gateway to this subnet so that outside world connectivity can be done and in Private subnet we will launch Mysql instance so that so that it can't connect to outside world.
# Creating Subnet :-Public Subnet
- Private Subnet
- Step-3
* Internet Gateway
An internet gateway serves two purposes: to provide a target in your VPC route tables for internet-routable traffic, and to perform network address translation (NAT) for instances that have been assigned public IPv4 addresses. An internet gateway supports IPv4 and IPv6 traffic.One VPC only have 1 Internet gateway. So for connection between Ec2 instance and public world we need 'Internet Gateway' and this thing can be full fill by router/modem.

# Creating Internet Gateway
Step-4
* Routing Table
Now we create route table .It is used to connect internet gateway and subnet then we associate this route table only to public subnet.

# Creating routing table and associating it to Internet Gateway
Step-5
* Creating a Key-pair
Here we create a key-pair. Basically a key pair is used to control login access to EC2 instances.You use the private key, instead of a password, to securely access your instances. Anyone who possesses your private keys can connect to your instances, so it's important that you store your private keys in a secure place.
# Saving Key to local file

After creating key-pair we save it into our local system for future use if needed.
Step-6
* Creating Security Group
A security group acts as a virtual firewall for our instance to control incoming and outgoing traffic. we can add rules to security group that allow traffic to or from its associated instances. Here we will create 2 security groups one for Wordpress i.e, "wordpress-sg" which allow http port 80 so that whenever client want to access wordpress can connect it via port 80, we also allow port 22 for SSH so that we can log in into our instance if needed and another security group is for MySql i.e, "mysql-sg" in this we will allow 3306 port to connect to MySql database .

Wordpress Security Group
MySql Security Group
In above Mysql security group named as "mysql-sg" we also write a rule i.e, allow only those Ec2 instances to access Mysql database which has wordpress security group named as "wordpress-sg" by doing this we are ensuring user's data privacy.
Step-7
* Launching Wordpress instance
Here we are launching ec2 instance from our own 'Wordpress ami'( which I created)this ami is already configured with Wordpress , also providing instance type, vpc id , key pair and most importantly subnet id as described above that wordpress will be launched in public subnet having internet gateway attached to it so that outside world connectivity can be done therefore it is launched in public subnet .
Step-8
* Launching MySQl instance
In this part we are launching ec2 instance from our own 'Mysql ami' which is already configured with Mysql database ,also providing all the necessary details i.e key pair , instance type, vpc id and most importantly subnet id as mentioned above that Mysql will be launched in private subnet so that no one can access it from outside world therefore it is launched in private subnet.
terraform init
This command is used to initialize a working directory containing terraform configuration files. This is first command that should be run after writing a new terraform configuration.
terraform apply
This command is used to apply the changes required to reach the desired state of the configuration .
terraform destroy
This command is used to destroy the terraform managed infrastructure. Run this command after finishing your work so that infrastructure can be closed .
* Conclusion:-
Finally Wordpress is launched on the top of Ec2 instance having MySql as a database ,before starting WordPress, provide the DB_HOST address to the WordPress configuration. So do SSH to WordPress instance and open the wp-config.php file present in the /var/www/html directory. Now provide the Private IP address of the database instance(Mysql) followed by:3306, as the DB_HOST from public ip of "WordPress" Ec2 instance we can access our wordpress site. And above all these things the main part is that to launch the whole infrastructure of aws in our own VPC which we have done successfully.
