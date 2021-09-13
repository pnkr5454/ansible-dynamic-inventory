# ansible-dynamic-inventory
## Step1: get-pip.py
This is a Python script that uses some bootstrapping logic to install pip.

Download the script, by using below command 

```
sudo wget https://bootstrap.pypa.io/pip/2.7/get-pip.py
```
Open a terminal/command prompt, cd to the folder containing the get-pip.py file and run:
```
python get-pip.py
```
## Step2: Install the boto3 and botocore
The below requirements are needed on the local controller node that executes this inventory.
1. boto3
2. botocore
```
pip install boto3
```
Note: It also installs botocore we dont want install botocore separately
## Step3: Install the aws ec2 plugin
```
ansible-galaxy collection install amazon.aws
```
## Step4: Write a YAML file to get dynamic hosts from aws
1. Get inventory hosts from Amazon Web Services EC2.
2. Uses a YAML configuration file that ends with aws_ec2.(yml|yaml).
3. Write a aws_ec2.yml file with below content
```
plugin: aws_ec2
# The values for profile, access key, secret key and token can be hardcoded like:
regions:
  - us-east-1
filters:
  # All instances with their `Environment` tag set to `dev`
  tag:Environment: dev
  # All dev and QA hosts
  tag:Environment:
    - dev
    - qa
```
## Step5: Create a IAM role from AWS with policy of adminstrative access and attach to ansible control machine 
## Step6: To know the host groups and apply the below command.
```
ansible-inventory -i aws_ec2.yml --list
```
## Run ansible with dynamic inventory
```
ansible all -i aws_ec2.yml -m yum -a 'name=git state=present' --private-key ./nodes.pem --become
```
