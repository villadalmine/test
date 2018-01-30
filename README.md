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
## PHASE1 AWS PROVISIONING
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


## PHASE2 AWS BASE CONFIG AND SOFTWARE
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



## PHASE3 AWS PROVISIONING
In this stage we deploy
```
 * We deploy locally prometheus + caddy + GRAFANA
 * Prometheus with ec2-discovery looks for deployed amazon instance using cadvisor and nodeexport
 * Using caddy to access from localhost to first discovered instance.
 * Using grafana with some templates for docker and host ammazon machine.

```


## PHASE4 AWS PROVISIONING
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
  roles/dockercompose/task/localprometheus
```

# Run the stack
```
[I] ~/v/r/r/a/t/playbook (master ⚡☡=) pwd
/Users/restaurador/violeta/repos/rino/aws/test/playbook
[I] ~/v/r/r/a/t/playbook (master ⚡☡=) ansible-playbook -i ec2.py  aws-provision.yaml  -vvv
```
For more info check the output of stack-creation.out.

# Remove the stack
```
[I] ~/v/r/r/a/t/playbook (master ⚡☡=) pwd
/Users/restaurador/violeta/repos/rino/aws/test/playbook
[I] ~/v/r/r/a/t/playbook (master ⚡☡=) ansible-playbook  remove-awsecs-stack.yml -vvvv

````
For more info check the output of stack-remove.out.
The local docker-compose will be deleted.

#TODO
Configure SSL/proxy or something that encrypt data where is necessary.
Cadvisor: Configure better authentication.(http basic or digest)
Prometheus nodeexporter/Cadvisor: create more custom rules , each collect rule as standard application rule in each case and for each OS in order to collect the same data for all enviroment.
Configure alerts if it is necessary.
Grafana: configure and create different dashboard accordly the enviroment.
Maybe a federation task to scale the infra will be an option.
Define how the apps works in each host and how we can scale it in order to determine how we can discover it and collect data to prometheus server.


### Used some links and examples
https://github.com/stefanprodan/dockprom
http://clavier.hatenablog.com/entry/2017/02/12/103505
https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config
