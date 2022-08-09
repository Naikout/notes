# DevOps learning:

# Orchestration:
	- Automation refers to a single tasks
	- Orchestration refers to the management of many automated tasks
	- Often a complicated ordering with dependencies

# Ansible notes:
	- Ansible system -> Control Node 
	- redhat.com or google for docs
	- playbook.yml, inventory.txt
	## hosts.txt
		- [webservers] <- group name
		- [dbservers] <- group name
		- [servers:children] <- Reference groups
		- webservers
		- dbservers
		
	- Ansible Galaxy (User-generated content)
	- Role is a package that can be plug-and-play executed in an ansible environment
	- ansible.cfg aka config file to modify paths and such
	## playbook sample.yml
		### name: sampleplaybook
		```
		  hosts: all
		  become: yes
		  become_user: root
		  tasks:
			- name ensure apache is at the latest version
			  yum:
				name: httpd
				state: latest
			- name: ensure apache is running
			  service:
				name: httpd
				state: started
		```
	- For installation check docs for guide
	- commands:
		### ansible myservers -m ping (Check servers)
			```
			ntiittan@Ubuntu-jammy:~$ ansible localhost -m ping
			[WARNING]: No inventory was parsed, only implicit localhost is available
			localhost | SUCCESS => {
				"changed": false,
				"ping": "pong"
			}
			```
		- ansible-playbook sample.yml
		- ansible-playbook orchestrate.yml -i myhosts
		- ansible all -m ping -i myhosts
		- -f 30 (forks) how many systems to execute upon

# Puppet notes:
	
	- Roles and profiles
	- vagrantup.com
	
	## Vagrantfile
		
		```
		CPUS="1"
		MEMORY="1024"
		
		Vagrant.configure("2") do |config|
		
			config.vm.box = "centos/7"
			config.vm.hostname = "master.puppet.vm"
			
			config.vm.provider "virtualbox" do |v|
				v.name = "master.puppet.vm"
				v.memory = MEMORY
				v.cpus = CPUS
			end
		
		end
		```
		
	## commands
		- vagrant up
		- vagrant ssh
		- sudo su -
		- rpm -Uvh https://yum.puppet.com/puppet6-release-el-7.noarch.rpm
		- yum install -y puppetserver vim git
		- vim /etc/sysconfig/puppetserver -> JAVA_ARGS Xms2gb (memory alloc)
		- systemctl start puppetserver (status, enable)
		- /etc/puppetlabs/puppet/puppet.conf -> host -> master.puppet.vm
		- add /opt/puppetlabs/puppet/bin to $PATH
		- exec bash
		- source .bash_profile
		- gem --help
		- gem install r10k
		- puppet agent -t
		- Change git branch master to production -> New branch production -> settings set production as default branch -> Save and del master
		- mkdir /etc/puppetlabs/r10k
		
		### vim /etc/puppetlabs/r10k/r10k.yml:
		```
		:cachedir: '/var/cache/r10k'

		:sources:
			:my-org:
				remote: 'https://github.com/user/repo.git'
				basedi: '/etc/puppetlabs/code/environments'
		```
		
		- r10k deploy environment -p <- get code from git
		
		### manifests/site.pp
			```
			node default {
				file {'root/README':
				  ensure  => file,
				  content => 'This is a readme',
				  owner   => 'root',
				}
			}
			```
		- r10k deploy environment -p <- get code from git
		- No duplicate declarations, for example two 'file' statements
	
	- Puppet forge check for packages (Check quality score and badge)
	
	## Puppetfile
	
		```
		mod 'puppet/nginx', '1.0.0'
		mod 'puppetlabs/stdlib'
		mod 'puppetlabs/concat'
		mod 'puppetlabs/translate'
		```
	
	## control_repo/site/role/manifests/web.pp
	
		```
		class profiel::web {
		  include nginx
		}
		```
	
	## control_repo/site/role/manifests/app_server.pp
	
		```
		class role::app_server {
		  include profile::web
		  include profile::base
		  include profile::app
		}
		```
	
	## control_repo/site/role/manifests/app_server.pp
		```
		class role::db_server {
		  include profile::base
		  include profile::db
		}
		```
	## control_repo/site/role/manifests/master_server.pp
		```
		
		class role::master_server {
		  include profile::base
		}
		```
	## control_repo/
	
		### environment.conf
		
		```
			modulepath = site:modules:$basemodulepath
		```
	## control_repo/site/role/manifests/site.pp
	
			```
			node default {
				file {'root/README':
				  ensure  => file,
				  content => 'This is a readme',
				  owner   => 'root',
				}
			}
			
			node 'master.puppe.vm' {
			  include role::master_server
			}
			```

	- puppetlabs docker
	
	## Modules
	
		### Manifests
			- Puppet code for your module
			- One class per manifest
			- The class named after the moduel is in init.pp
			- Example: the nginx class in the nginx modules is in manifests/init.pp
		
		### Files
			- Static files such as conf
		
		### Templates
			- Dynamic templates
		
		### Lib
			- Additional code (features)
		
		### Task
			- Ad hoc tasks (puppet bolt)
		
		### Other
			- Examples
			- spec
		
		### metadata.json
			- Fills in the details of the module
		
		### README.md
			- Documentation
			
		Puppet development kit -> puppetlabs pdk

# Docker notes:
	
	- docker ps -l or -a 
	- docker images -a
	- docker rmi 
	- MAINTAINER, VOLUME, RUN, ADD, CMD, WORKDIR, EXPOSE, USER, ENTRYPOINT, FROM, COPY
	- docker info
	- Docker uses bridges to create networks
	- docker run -it --rm --net=host
	- apt update && apt install bridge-utils
	- docker network create my-new-network
	- uses iptables fw rules
	- sudo iptables -n -L -t nat
	- docker run -p 8080:8080 portforwarding container -> host
	- docker inspect --format '{{.State.Pid}}' hello -> 7368
	- docker run --privileged=true --pid=host
	- kill 7368 -> kills container process
	- docker secret is CopyOnWrite
	- mount -o bind other-work work
	- umount work
	- ls -R
	- docker save & load -> migrating between storage types and shipping images on disks
	- docker compose up
	- Google kubernets or AWS kubernetes

# Chef notes:
	
	- Building, deploying, automating
	- CentOS provisioned via AWS
	- Chef uses Ruby
	- laptop <-> Vagrant
	- laptop <-> Server <-> Nodes
	- Chef DK
	
	## Config management
	
		- Application of programmatic methods that create consistency in the performance, function, design and operation of compute resources
		- Applied over the life cycle of a system
		- Verification of performance through testing
		- Use of pre-configured code to accomplish common tasks
		- Cost avoidance, prevent catastrophes, and time saves
		- CM template includes instance of public cloud, db, users
		- CM template can be used in multiple servers
		- Template can call creds from db
		- Rightscale, salt, CFEngine other similar tools
		- Should analyze the state of a server before making changes
		- Programmatic Configuration Management
		- Compatible with nearly any cloud platform or datacenter
		- Useful for cloud deployment
		- Large scale automation of compute resources
		- Can use the public clouds API
		
	## Infrastructure management
	
		- System resources
		- Infrastructure becomes versionable
		- Workstation - develop and test
		- Chef server - upload code to server
		- Nodes - distributed runners of chef code
		
		### Workstation
		
			- Manage server and nodes
			- Environment variables to path
			
			#### Install Chef DK
			
				- Available for windows
			
			#### --version command for:
			
				- chef
				- chef-client
				- knife
				- ohai
				- berks
				- kitchen
				- foodcritic
				- cookstyle
			- Vagrant and virtualbox
			- SSH client
			- vagrant box add bento/centos-7.2
			- vagrant init bento/centos
			- vagrant up
			- vagrant ssh
			- Install chefdk using curl omnitruck.chef
			
			#### Common vagrant commands
			
				- ssh-config | Displays connection details
				- init bento/centos-7.2| Creates a vagrant file
				- status | Lists VMs and their status
				- suspend | Saves VM state and shuts down
				- destroy --force | Destroys all running VMs
				- box add bento/centos-7.2. --provider=virtualbox | provision vagrant machine centos for vbox
				
			#### VM Centos
			
				- curl and install chefdk and text editor
				
				```
				file 'hello.txt' do 
					content 'Hello, world!'
				end
				```
		### Chef server
		
			- Stores cookbooks, roles, environments and policies needed
			- indexes metadata about nodes
			- acts as a pull server for nodes
		### Nodes
		
			- Nodes use chef client to pull policy from the chef server aka convergence
			- Run system inventory and gather host details with the Ohai tool
			
		### Takeaways
		
			- Resource
			- Recipe
			- Cookbook
			- Node object attributes
			
	- Uses Git
	
	
	
# Jenkins:

	## jenkins as a docker container:
	
		docker --version
		docker pull jenkins/jenkins:lts
		docker run --detach --publish 8080:8080 --volume jenkins_home:/var/jenkins_home --name jenkins jenkins/jenkins:lts
		docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
		
	## Add Maven
	
		- Name it
		- Apply it
	## Job:
	
		- Job -> Github hook trigger for GITScm polling
		- Job -> Poll SCM
		- CleanWS() or Job -> Delete workspace before build starts
		- Use secret files
		- Abort the build if it's stuck
		- Add timestamps to the console output

# Kubernetes:

	- minikube, kind, kubernetes desktop
	- .yaml files to create deployments
	- kubectl get pods --show-labels
	- kubectl label po/helloworld app=helloworldapp --overwrite
	- kubectl label pod/helloworld app-
	- kubectl get pods --selector env=production --show-labels
	- kubectl get pods --selector dev-lead=carisa,env=staging
	- kubectl get pods -l 'release-version in (1.0.2.0)' --show-labels
	- kubectl delete pods -l dev-lead=karthik -> Pods "Terminating"
	- kubectl create -f helloworld-with-probes.yaml
	- kubectl get deployments
	- kubectl describe <pod> -> Events: warning etc.
	- kubectl rollout history
	- kubectl describe deployment bad-helloworld-deployment -> Events: normal -> kubectl get pods -> kubectl describe po/<pod> -> Events: Warning
	- kubectl logs <pod>
	- kubectl exec -it helloworld-deployment /bin/bash -> ps -ef -> exit -> exec -c helloworld
	- minikube addons list & minikube addons enable dashboard -> metrics-server
	- minikube dashboard -> Browser -> Labels, metrics, replicasets, events, pods, config, workloads, create apps
	- kubectl create configmap logger --from-literal=log_level=debug -> configmap "logger" created
	- kubectl create -f reader-configmap-deployment.yaml
	- kubectl get configmaps -> get configmap/logger -o yaml
	- kubectl get deployments -> logreader-dynamic-123123-12312	
	- kubectl create secret generic apikey --from-literal=api_key=123123123
	- kubectl get secret apikey -o yaml
	- secretKeyRef
	- kubectl get jobs
	- cron to run jobs periodically -> kubectl get cronjob -> SCHEDULE */1 * * * *
	- kubectl edit cronjobs/hellocron -> suspend: false -> true
	- daemonset using busybox
	- kubectl get daemonsets
	- zk=zookeeper apache key value store
	- kubectl get statefulsets
	- Kubernetes the Hard Way Kelsey Hightower
	- kubeadm
	- Flannel and Weave Net for Pod Networks
	- kops for AWS cluster provisioning
	- Cloud services have native klustering systems
	- Managed kubernetes vs self install -> control vs use case
	
	## Namespaces:
		- Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces.
		- For dev test and prod, different usergroups etc.
		- kubectl get namespaces
		- kubectl create/delete -n namespace-name namespace
		
	## Logs:
		- stdout because kubectl logs will fetch them
		- Elastic search for kube service and kibana 
		
	## Monitoring:
		- cAdvisor open source resource collector
		- Prometheus open source system monitoring and alerting toolkit
		- Above tools typically linked to grafana
		- Datadog and Splunk also
		
	## Authentication:
	
		- Normal users (humans)
		- Service accounts managed by K8s API
		- Define user with username, uid, group, extra fields
		- Modules: client certs, static token files, openID Connect, Webhook mode using kube-apiserver
		- ABAC: Attribute-based access control (parameter does have authorization or not)
		- RBAC: Role-based access control (Role has authorization or not)
		- Webhook (Check using kube-apiserver who has permissions)
		
# DevOps foundations - CI/CD notes:

	- CD -> Integration tests after/during deployment
	
	## Benefits:
	
		- Teamwork is better
		- Cycle times are lower
		- Better security
		- Rhytm of practice
		- More time to be productive
		- Build pipeline -> source code, tools, tests, artifacts, deployment, ci testing, production (+ linters, security testers etc.)
		
	## Pipeline example
	
		### Version Control using Github
		
			- Always use version control
			- Always be able to deploy dev/prod version using one command (makefile?)
			- Easy-to-understand commit messages
			- Master branch with development or issue branches so that commits aren't too big
			- Commit hooks for testing changes (gitlab-ci.yml for example)
			- Careful with secrets
			- CircleCI for webhooks
			
		### Test Automation using Jenkins
		
			- Saas-based offering or Open Source
			- CI is a practice not a tool
			- Start with a clean environment
			- Pass the coffee test -> Tests should pass in the time that it takes to get a cup of coffee
			- Test locally before pushing
			- Don't commit new code to broken builds
			- Don't leave the build broken
			- Don't remove failing tests
			- Notifications from test pipelines if they fail or take over 1 hour
			- jenkins docker with build-essentials, ansible, sshpass
			- docker-compose.yml to start jenkins containers -> docker-compose up --build -d
			- Poll SCM to check for changes in Git
			- Build -> Execute Shell
			- Works on the concept of plugins
			- plugins.jenkins.io
			- Don't install too many plugins so that jenkins master doesn't have too much downtime due to updates
			- Job to get code from Git, zip it and send to Nexus
			
		### Artifactory using Nexus
		
			- Reliability, security, composability, shareability
			- Build it, test it and package it
			- Security, by authorization to artifactory so that code doesn't get sneaked in
			- Artifactory repo let's users know what is packaged and can be used with what versions
			- sonatype.com -> nexus repository opensourcesoftware
			
			#### Paid:
				-JFrog Artifactory
				
			#### Free:
			
				- Nexus repository manager 3
				- Apache Archiva
				- Many specific for for example docker containers, npm, .NET packages etc.
				
			- docker-compose up docker.compose.yml nexus
			- localhost:8081 or whatever set in docker-compose.yml
			- Browse by components and assets
			- Repositories -> Select Recipe
			- Jenkins plugin for Nexus
			- Nexus details for jenkins job
			
			
		### Testing
		
			- makefile -> make test etc.
			- Fast, reliable, isolate failures
			- Unit (Junit, Xunit, Rspec), integration (Full application, API, Abao, RAML, Serverspec), e2e/ui(Selenium), security testing (Fortify, Findbugs, Gauntlt), Performance testing (System tests etc.)
			- Shift left meaning move testing to the beginning such as local full tests
			- Test fixture (set of objects to run tests in a well-known environment (managed like artifacts))
			- System under test -> The application and system which you are running your tests
			- Lead time (Time taken from issue to production)
			- Mock classes that contain external dependencies for unittests
			
			#### Philosophy:
			
				- Test-Driven Development write failing tests before coding
				- Behavior-Driven Development writing tests in a simple end-user-behavior-centric language (like robotframework? Cucumber)
				- Acceptance test-driven development, the practice of agreeing on acceptance tests before development to establish what is to be delivered
				##### Big three testing metrics:
				
					- Cycle time (time from the start of work to delivery)
					- Velocity (value delivered per unit time)
					- Customer satisfaction (How well a product/service met the customer's needs. NPS score and bug reports work well here.)
					- Be strict, test early, invest appropriately (Google guide is 70% E2E, 20% Integration, 10% Unit)
				
			#### Unittests:
				- Import testable module
				- Fill arguments with mock data
				- Check that test parameters are filled
				- Test valid / invalid scenarios
				
		### Deploy using Chef
			- Deploy with the same artifact, the same way, same (similar) environment, the same smoke tests -> tag smoke in robot that can be run in production
			- Deploy small batches
			- Keep changes loosely coupled to each other
			- Basic api versioning and schema versioning
			
			#### Separate deploy and release
			
				- Blue/green deployments (publish to one machine at a time)
				- Feature flags to check if problems with said feature and easy to toggle on/off
				- Canary deployment
				
			#### Deployment tools
			
				- Source pulls
				- CM systems Chef or puppet
				- Orchestration push  like ansible
				- Build like docker
				- and many more Commercial options
				- Custom deploy option
				
			#### Product update deploys to customer
			
				- Debian container -> Update server -> Rundeck orchestration server -> Puppet installation
				
			#### Deployment using ansible
			
				- Dockerfile Ubuntu 20 container
				- docker-compose.yml with jenkins
				- docker-compose up --build -d -> jenkins
				- Ansible plugin for jenkins
				- Install ansible and sshpass for jenkins
				- ansible playbooks in jenkins_home/ansible/
				- Minimun functional set
				- tasks.yml
				- download package from artifactory -> unzip package -> deploy server
				- Jenkins job deploy -> Invoke Ansible Playbook 
				- Build jenkins job with version
				- Log into testfixture docker container
				- Check if server is running
				- go to localhost:port to check if server is online
				- Downgrade or upgrade using jenkins job and specifying version
				- Build system poll from artifactory and deploy using read-access system
				
			#### Integration testing:
			
				- Testing performed to expose defects in the interfaces and in the interactions between integrated components or systems
				- make run -> localhost:port
				- curl response of server app and create a test for it
				
			#### UI tests:
			
				- Regression testing
					- A test conducted to verify that software which was previously developed and tested still performs the same way after it is changed or interfaced with other software
				- Acceptance testing
					- A test conducted to determine if the requirements of a specification or contract are met
				- E2E testing
					- Testing and applications workflow from beginning to end
				- Selenium -> webdriver to test web apps
				- Robotframework BDD with SeleniumLibrary plugin and PageObject in an encapsulated resource model
				- Robot env using Python-virtual-evironment
				- webdrivermanager
				- robot . <- must find tests
				- log.html
				
			#### Security tests:
			
				##### Dynamic
				
					###### Pros
					
						- Test actual attack payloads
						- Collaboration between developers, security and operations
						
					####### Cons
					
						- Requires system to be running
						- Can be slow
						
					####### Gauntlt
					
						- What (look for cross site scripting using arachni), given (arachni is installed), when (when I launch arachni), then (arachni -check=xss* <url> -> 0 attacks should be found)
						- gauntlt docker github
						- git clone, make build, make install
						- test against docker container
						- gauntlt-docker attack
					###### retire
						- npm install retire
						- retire -j -> static analysis of requirements
						
			#### Best practices:
			
				- Each developer is responsible for their own changes
				- Small changes
				- Don't check in broken builds (take responsibility, check that build succeeds, revert prior version)
				- Automate high quality testing
				- Run tests before check in
				- fix flaky tests
				- Dont ignore tests
				- Keep build clean and tested
				- QA for high value activity
				- Automate deployments
				- Keep the build and deploy fast
				- Crazy fast build times -> remove time waste from build cycle
				
				##### IRL
				
					- Teach people to imagine a good CI/CD pipeline
					- Changing process
					- Look at total cycle time and attempt to minimize
					- Balance benefits of fast feedback
					- Builds are not reliable -> Too many tests are bad and ruin build quality
					- Deploys take a long time
					- Tradeoffs
					- Just do it
					- Take responsibility and test all steps
					- Have gumption
