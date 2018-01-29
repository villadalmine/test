# Deployment of prometheus+grafana+caddy+nodeexport+wordpress
We have 4 stage to run the stack.

## Vars file to use:
Those are my personal values:

```
 * vars_files/aws-aws-vars.yml
   stack_name: "Aws-ECS"
   stack_state: "present"
   aws_region: "us-east-2"
   user: "ec2-user"
   newuser: "restaurador"
   sshcfgpath: "/Users/restaurador/violeta/repos/rino/aws/test/playbook/ssh.cfg"
   commandec2: "Users/restaurador/violeta/repos/rino/aws/test/playbook/ec2.py"
   pathkeys: "/Users/restaurador/violeta/repos/rino/aws/test/playbook/keys/private/RINO_OHIO.pem"
   password: "$6$rounds=656000$SomeSalt$nCXtF9kt3CX5oIDHJRxFhzp9bMzpP0p63huFgQX8WIIa5f2XfQ8QVJMhAMM4BAPuCnvnOfMKpCOOiHR/RY0C41"
   rootp: "$6$rounds=656000$SomeSalt$nCXtF9kt3CX5oIDHJRxFhzp9bMzpP0p63huFgQX8WIIa5f2XfQ8QVJMhAMM4BAPuCnvnOfMKpCOOiHR/RY0C41"
   wordpressdbdeploy: True
   nodeexportercadvisor: True
   wordpresdbpassword: xxxxxx
   mysqlrootpassword: xxxxxx
   rolepath: "/Users/restaurador/violeta/repos/rino/aws/test/playbook"

 * roles/distrostack/files/awsecs-wordpress.yml
   Please check the value of instance, zone, disk and others.

 * vars_files/aws.yml
   package_manager: yum
   tools_install:
     - docker
     - mc
     - git
   with_tools: True

 * vars_files/key.example
   You need to put your amazon key there
 * vars_files/aws_private.yml
   You need to put your credentials there.
   aws_region: us-east-2
   # If you choose to set specifically, ENV variables are usable
   aws_access_key: xxxxx
   aws_secret_key: xxxx
   aws_amzn_linux_ami_id: ami-710e2414
   aws_private_key_dir: /Users/`whoami`/tmp

```
##PHASE1 AWS PROVISIONING
In this phase we create:
```
 * Aws instance using CloudFormation
   roles/distrostack/files/awsecs-wordpress.yml
 * Vars files needed
   vars_files/aws-aws-vars.yml
   keys/private/aws_private.yml
 * roles/base/templates/sshcfg.yml
   In this file we configure ssh login in order to use ec2.py
 * We use ec2.py for dynamic host also have edited ec2.ini to filter searching
```

You will have ready your instance in us-east-2


##PHASE2 AWS BASE CONFIG AND SOFTWARE
In this step we install and configure:
```
 * Configure users and update password
 * Configure and install docker and docker-compose
 * Install and upgrade some programs for OS

 * Vars files needed
   vars_files/aws-aws-vars.yml
   keys/private/aws_private.yml
   vars_files/aws.yml
```



##PHASE3 AWS PROVISIONING
In this stage we deploy
```
 * We deploy locally prometheus + caddy + GRAFANA
 * Prometheus with ec2-discovery looks for deployed amazon instance using cadvisor and nodeexport
 * Using caddy to access from localhost to first discovered instance.
 * Using grafana with some templates for docker and host ammazon machine.

```


##PHASE4 AWS PROVISIONING
In this stage we deploy :
```
 * Wordpress with docker-compose
 * Nodeexpert and cadvisor with docker-compose
 * Collect data from host and docker using previous docker
 * Expose services to internet in order to send to local prometheus using ec2-discovery

* Vars files needed
  vars_files/aws-aws-vars.yml
  keys/private/aws_private.yml
  vars_files/aws.yml
  roles/dockercompose
```
