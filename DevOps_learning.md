# DevOps learning

# Orchestration

- Automation refers to a single tasks
- Orchestration refers to the management of many automated tasks
- Often a complicated ordering with dependencies

# Ansible notes:
	
- Ansible system -> Control Node 
- redhat.com or google for docs
- playbook.yml, inventory.txt
- Ansible Galaxy (User-generated content)
- Role is a package that can be plug-and-play executed in an ansible environment
- ansible.cfg aka config file to modify paths and such
	
## hosts.txt
- [webservers] <- group name
- [dbservers] <- group name
- [servers:children] <- Reference groups
- webservers
- dbservers
		

	
## playbook sample.yml

name: sampleplaybook
		
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
	mode 0644
	owner 'root'
	group 'root'
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
- sudo chef-client --local-mode hello.rb
- Resources take action on properties
- Principle of least surprise

## Test and repair
		
- chef-client takes action only when it needs to. Thin of it as test and repair
- Chef looks at the current state of each resource and takes action only when that resource is out of policy
- Changing permissions on chef made file requires recipe policies
- docs.chef.io

## Webserver

- chef generate --help
- install tree and check directory structure
- readme description of cookbook features
- metadata.rb as a table -> name, use, maintainer contact info
- default.rb
- spec used for unittests and test used for integration tests
- .git directory
		
### Chef resources

- Apache server
- creates a directory /var/www/html
- port 80
- httpd service
- curl localhost
- chef generate recipe cookbooks/apache server
- tree cookbooks/apache/

#### Apache recipe

```
package 'httpd' 

file 'var/www/html/index.html' do
content '<h1>Hello, world!</h1>'
end

service 'httpd' do
action [:start, :enable]
end
```

- sudo chef-client --local-mode cookbooks/apache/recipes/server.rb
- --runlist tells what recipes and cookbooks need to be ran and in which order
- sudo chef-client --local-mode -zr "recipe[apache::server]"
#### include_recipe
			
- A recipe can include one or more recipes located in cookbooks by using the include_recipe method
- When a recipe is included, the resources found in that recipe will be inserted sequentially at the point where the include_recipe keyword is located.
- include_recipe 'apache::server'
- ^ into default recipe so that apache is always started by default
- Chef resources are not pure Ruby code and cannot be run in a Ruby intrepreter
- Learn resources

### Details from node

- hostname -I
- cat /proc/meminfo
- Node has attributes and they are split into parent and child elements
- Insert node info into apache index.html
- String interpolation with "I have #{apple_count} apples" <- basically python format
- <h2>ipaddress: #{node['ipaddress']}</h2>
		
### ohai

- Shows host information in json
- ohai ipaddress, hostname, memory
- chef-client and chef automatically run ohai
			
### ERB

- Embedded Ruby template allows Ruby code to be embedded inside a text file within specially formatted tags

```
<% if (50 + 50) == 100 %>
50 + 50 = <% 50 + 50 %>
<% else %>
At some point all of MATH I learned in school changed.
<% end %>
```
		
### Set up load balancer and web servers


Web Server | Load balancer
--- | ---
Provision the instance | Create the haproxy (load balancer) cookbook
Install Chef | Provision the instance
Copy the web server cookbook | Install Chef
Apply the cookbook | Copy the haproxy cookbook
None | Apply the cookbook

### Flavors of Chef Server

- Open source Chef server
- Chef Server (Support + Premium Features)
- Multi-Tentant Hosted Chef Server

### Upload cookbook to webserver

- knife cookbook upload <cookbook name>
- knife cookbook list to see what is uploaded to chefserver

### Nodes

- knife bootstrap <ip address> -x chef -P chef --sudo -N web1 -r "recip[apache]"
- sudo chef-client -> check sudoers
- knife node list -> list nodes
- knife node show <node1> -a ipaddress -> details of node and attribute ipaddress
			
### Test cookbooks with test kitchen

- Automated way to ensure code accomplishes what we expect it to do without fail

#### Steps to verify cookbooks

- Create virtual machine
- install chef tools
- Copy cookbooks
- Run/apply cookbooks
- Verify assumptions
- Destroy virtual machine

Kitchen | Steps | .kitchen.yml
--- | --- | ---
kitchen create | Create virtual Machine | driver
kitchen converge | Install Chef Tools | provisioner
kitchen converge | Copy cookbooks     | provisioner
kitchen converge | Run/ Apply cookbooks | provisioner
kitchen verify | verify assumptions | busser
kitchen destroy | Destroy virtual machine | -


- kitchen --help
- kitchen list -> Last action = <not created>
- kitchen create -> list -> Last action = <created>
- kitchen converge -> list -> Last action = <converged>
- which httpd
- curl localhost

#### Testing using Inspec
	
- test/recipes/default_test.rb
- Developed by Chef and essential to check if changes break anything
- More info in docs

```
describe port(80) do
	it { should be listening }
end
	
describe command('curl localhost') do
	its(:stdout) { should match (/Hello, world!/) }
end
```
	
- ^ kitchen verify
		
#### manage.chef.io
	
- Administration -> Organization -> Members
- Policy to see cookbooks and version
- Nodes -> see all nodes with platform, fqdn, ip address, uptime, last check-in, environment, actions
- Clicking a node shows details, attributes and versions
- Ohi tool gathers information to node page
 	
### Community cookbooks
	
- supermarket.chef.io
- github of all thing chef
- chef does not verify or approve cookbooks in supermarket
- Benefits from community code is to learn about chef and know what is possible
- Hosted in github fully

#### Wrapper cookbooks

- A wrapper cookbook encapsulates the functionality of the original cookbook
- It defines new default values for the recipes
- chef generate cookbook cookbooks/myhaproxy <- my version of community cookbook
- Set up depends into default recipe with version
- in metadata file -> depends 'haproxy', '= 1.6.7'
- ruby hash or ruby object key value pair
- 'hostname' => 'asdasd.com'
- include_recipe 'haproxy::default'

### Berkshelf

- Is a cookbook management tool that allows you to upload your cookbook and all of its dependencies to the chef server
- berks --help
- berks install
- Berksfile
	
```
source 'https://supermarket.chef.io'
	
metadata
```

- ls .berkshelf/cookbooks
- knife cookbook list
- knife bootstrap

#### Load balancer
	
- Load balancer balances load between servers
- Adding a load balancer will allow you to better grow your infrastructure
- A load balancer receives requests and relays them to other systems
- knife node show load-balancer
	
#### Roles

- A role describes a run list of recipes that are executed on the node
- A role may also define new defaults or overrides for existing cookbook attribute values
- When you assing a role to a node, you do so in its run list
- This allows you to configure many nodes in a similar fashion
- ~/chef-repo/roles/web.rb
	
```
name 'web'
description 'Web server'
run_list 'recipe[apache]'
```
	
#### Environments

- Environments can define different functions of nodes that live on the same system
- Production and acceptance nodes

```
name 'production'
description 'Where we run production code'

cookbook 'apache', '= 0.1.0'
cookbook 'myhaproxy', '= 0.1.0'
```
knife bootstrap ... -r "role[web]" -E production

#### Data Bags

- a data bag is a container for items that represent information about your infrastructure that is not tied to a single node
- list users and groups or passwords and let nodes access
- Json
- knife search users "id:bobo"


# Jenkins notes

## jenkins as a docker container

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

# Version Control

- automatic backups
- change-by-change log of your work
- both short-term and long-term
- easy to create a 'sandbox' to try new things
- rollbacks are easy
- labeling significant changes

## How do teams benefit from version control?

- Synchronization - easy to keep team members always up-to-date
- accountability - know who made each change and why
- conflict detection - keep the build clean every time

## Essential

- repository for file storage and history
- working set is the current state of files as stored on your local machine
- add files to commit
- commit to check in added files
- checkout update to copy changes from repo to local
- tag to mark the current state of the repo for future checkout
- revert / rollback to fix changes by overwrriting files in working set

## Getting started

- Init repo
- and main func to repo with commit
- add func with second commit
- git log --oneline --all
- git diff <commit> <commit>

## History

Centralized
---

- SCCS 1972 from bell labs
- RCS 1982
- CVS 1986 file-focused first central repository
- perforce 1995 still the biggest repository inside Google (in 2012)
- subversion 2000
- Microsoft Team Foundation Server 2010 comes with MSDN subscription

Distributed
---

- Git 2005 created by Linus Torvalds after BitKeeper
- Mercurial 2005 also created in response to BitKeeper change

## Terminology

- push / export -> send changes from one repo to another (distributed only)
- pull / import -> update your working set with updates (distributed only)
- branch / fork -> clone of a repo
- merge -> integrate your branch back to the original repo
- reverse integration / forward integration -> branching by features

## Concepts

- Init / create repo
- Add files
- Commit / Stage files
- Files to repo
- Checkout or update changes to working set
- Shelf set aka stash
- Branches if it is lightweight such as mercurial and git
- Revert / rollback to specific version
- tags / labeling is a human readable name for feature versions

## Subversion

- SVN
- Centralized
- Free and commercial hosting options
- Setup environment for commit message text editor
- svn mkdir
- svn import <path>
- svn co <file>
- svn status
- svn add <file>
- svn commit -m "message"
- svn update (-r <revision name>)
- svn log (-q)
- svn ci
- svn diff (-r <revision name>)
- svn revert
- svn copy trunk tags/v1
- svn copy trunk branches\b1
- svn merge -r6:7 branches\b1 trunk
- TortoiseSVN


## Perforce

- File depot
- Mark for add
- Submit -> changelist description
- Refresh
- Folder History
- Time-lapse view aka blame or history
- Diff against have revision
- Revert
- Rollback to revision, changelist, date/time, label
- label
- depot/branch1/ + refresh
- checkout and open
- Merge/Integrate -> Options -> Automatically resolve changes

## Microsoft Team Foundation Server

- Centralized
- free (for open-source projects) and commercial
- First create team project then folders
- Define process template -> Visual Studio Scrum
- Create an empty folder
- New project -> Console Application -> Location
- Add solution to source control from solution explorer
- There should now be a folder in the team folder
- Team explorer -> Pending changes -> comment -> check in confirmation
- Add class -> File has + indicating it is new to be added
- Solution explorer -> Compare
- View history -> Get this version / rollback
- New label with name, comment and version
- Changesets and labels tabs
- Branches are new folders inside current folder
- Source Control Merge Wizard with source branch and target branch as options

## Git

- Distributed
- free with commercial hosting options
- open-source free bits
- free hosting for open-source on github
- make directory

Commands
---
- init (initialize repo)
- status (See current status of changes)
- diff (See changes of files)
- checkout (go to branches, commits)
- add (Add file for to be committed)
- commit (Commit files with message of changes and changeset id) -a (open text editor) -m (Commit message in quotations)
- log (Check previous commits)
- tag -a <version> -m <name>
- push (push changes to branch in repo)
- pull (pull changes from repo)
- merge (merge changes from target to current)
- rebase (Rebase commit-by-commit) 
	
## Mercurial
	
- Distributed
- free with commercial options
- Config user via mercurial.ini with email
- TortoiseHG
	
Commands
---
- hg
- --version
- init
- status
- add
- commit (-m)
- log (-v)
- revert (-r)
- tag(s)
- branch
	
## Lean and agile in devops

- Agile manifesto because software development failed too often
- Jidoka Autonomation; intelligent or humanized automation
- Greenfield team
- Backlog with task, assigned to and scheduler
- Iteration via sprints
- Theory of constraints
- Minimum viable product
- Nemawashi -> Informally and quietly laying the groundwork for a proposed change or project, by talking to the people concerned to gain their support
- Master / Trunk flow
- Code reviews
- Prioritized workflow
- Goals for every iteration
- Per ticket sign-off procedure
- Go/no-go meeting
- Peer review for pull reviews
- Automated testing
- Scrum master aka he facilitator for a product development team that uses scrum, responsible for coaching the team on scrum practices
- Time to market lower than others
- Refactoring aka restructuring existing software code without changing its external behavior
- Using source control allows the building and testing of your infrastructure automation within your CI pipeline
- Burndown charts for sprints, epics and release to check progress
- Velocity chart tracking
- WIP, throughput, lead and cycle times
- Don't weaponize metrics so that teams don't turn on each other
- The phoenix project and leading the transformation books
- Design reviews with mixed teams of devs and ops
- End-of-iterartion demos
- Discontinuation of ops team when DevOps is successful
- Manual tickets = Lost efficiency
- Backlog full of requirements to user stories which is a requirements written from the pov of a user and the functionality they desire
- Personas which are definitions of an archetypal user of the system
- Service-level Agreement SLA is an agreed level of service as measured by a specific metric
- Visibility first approach
- Key performance indicators aka metrics used to determine how effectively you're meeting your objectives
- Continuous learning by using Kaizen which is Change + Better = Continuous Improvement
- Improvement Kata in TPS is Plan -> Do -> Check -> Act -> Plan...
- Merit badge system earn badges by taking courses or tests held by experts in subjects
- Work-life balance
- Reward hard work by letting team off early after sprint demos and occasionally on fridays
- Pilot new projects where you can greenfield teams
- Try something you think will work for your organization
- Measure results
- Adjust
- Iterate
- Empower project teams instead of dictatoral leading
- Align teams on common processes, tools, and work cultures to facilitate common change
- The hardest part of all this is changing the culture the way people are used to working and behaving
- Don't get carried away to avoid waste

Lean UX
---
- Manifesto: "We are developing a way to create digital experiences that are valued by our end users. Through this work, we hold in high regard the following"
- Early customer validation over releasing products with unknown end-user value
- Collaborative design over designing on an island
- Solving user problems over designing for the next cool feature
- Measuring KPIs over undefined success metrics
- Applying approriate tools over following a rigid plan
- Nimble design over heavy wireframes, comps or specs
- As stated in the Agile Manifest, "While there is value in the items on the right, we value the items on the left more"
- Early user input
- Frequent user feedback
- Continued iteration
- Prioritize prototyping

Books
---
- Lean UX: Applying Lean Principles to Improve User Experience
- UX For Lean Startups: Faster, Smarter User Experience Research and Design
- Running Lean: Iterate from Plan A to a plan that works
- Lean Enterprise: How High Performance Organizations Innovate at Scale
- Lean Customer Development: Build products your customers will buy
- Lean Branding: Creating dynamic brands to generate conversion
- Lean Analytics: Use data to build a better startup faster
- Lean IT: Enabling and sustaining your lean transformation
- Lean Software Development: An agile toolkit
- The Goal: A process of ongoing improvement
- The Lean Startup: How today's entrepreneurs use continuous innovation to create radically successful businesses
- The Phoenix Project
- The DevOps Handbook

ChatOps
---
- Shared chat channels
- Commands executed in chat
- Chatbot then runs scripts
- ChatOps: Managing Operations in Group Chat - Jason Hand 2016
- Commits, test results, merge requests, smoke tests, status info, run deployments
	
Common processes
---
- Same IDE
- Same ticketing system
- Same testing requirements
	
As you scale
---
- Reduce barriers to communication
- Scaled Agile Framework (SAFe) can help reduce complexity
- Goals and expectations should be clear
- Empower your teams
- Mistakes are expected, as are learning opportunities

	
(Melvin) Conway's Law
---
	> Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations

SLA example
|---|
Service usage, such as visitors or new users
Service performance
Service availability
Delivery speed/cycle time
Customer support/bugs reported
Percentage of our time spent on new features versus maintenance
	
Blameless post-mortems instead of blaming for faults
|---|
A description of the incident
A description of the root cause
How the incident was stabilized or fixed
A timeline of events, including all actions taken to resovle the incident
How the incident affected customers
Remediations and corrective actions

12 principles
---
1. Our highest priority is to satisfy the customer through early and continous deliver of valuable software
2. Welcome changing development
3. Deliver working software frequently
4. Business people and developers must work together daily throughout the project
5. Build projects around motivated individuals. Give them the environment and support they need, and trust them to get the job done.
6. The most efficient and effective method of conveying information to and within a development team is face-to-face conversation.
7. Working software is the primary measure of progress
8. Agile processes promote sustainable development. The sponsors, developers, and users should be able to maintain a constant pace indefinitely
9. Continuous attention technical excellence and good design enhances agility
10. Simplicity - the art of maximizing the amount of work not done-is essential
11. The best architectures, requirements, and designs emerge from self-organizing teams
12. At regular intervals, the team reflects on how to become more effective, then tunes and adjusts its behavior accordingly

- Visibility and constant delivery of business value
- Owners always know how to project is progressing
- Deliberately more variable
- Frequent communication

Methodologies | Summary
--- | ---
Extreme Programming (XP) | N/A
Scrum | Product backlog + Grooming -> Planning -> Sprint Backlog -> Sprint (2-4 weeks) with stand-up aka daily scrum -> Valuable product
Feature-driven development (FDD) | N/A
Rational unified process (RUP) | N/A
Crystal | N/A
Lean | N/A
Adaptive software development (ASD) | N/A
Kanban | Visualize the workflow, Limit work in progress, Manage the flow, Make process policies explicit, Improve collaboratively + kanban board
Dynamic Systems Development Method (DSDM) | N/A
Scrumban | N/A

- Value stream map to show value in organization
- Muda (Waste) work that absorbs resources but adds no value
- Mura (Unevenness) work coming in unevenly instead of a constant or regular flow
- Muri (Unreasonable) work imposed on workers and machines
- Kaizen continuous improvement

Lean Principles
---
- Small deliverables and limiting work in progress
- Information radiators and visibility into flow
- Gathering, broadcasting, and implementing customer feedback
- Empowered development teams free to experiment and improvement


Build-Measure-Learn
---
- Build minimum viable product
- Measure outcome and internal metrics
- Learn about your problem and your solution
- Repeat go deeper where it's needed

## Lean Technology Strategy: Building High-Performing Teams	
- Invest in your people
- Job satisfaction is the most important indicator of performance
- How to deal with failure
- Low performance workplace finds person to blame
- High performance learns from mistakes without blaming
- At the Toyota plant you can take time with the car by pulling on a line and a manager comes to help you
- Change culture (Give better tools, teach how to do things better)
- Machines do the boring repetetive tasks and people do the techincal problems

> The talent myth assumes that people make organizations smart. More often than not, it's the other way around... Our lives are so obviously enriched by individual brilliance. Groups don't write great novels, and a committee didn't come up with the theory of relativity. But companies work by different rules. They don't just create; they execute and compete and coordinate the efforts of many different people, and the organizations that are most successful at that task are the ones where the system is the star. - Malcolm Gladwell

- Fixed mindset vs Growth mindset
- 
	
### Autonomous Teams
- Give teams the tools and authority to choose tools and ush changes to production
- Ensure teams have the skills and authority to design, run, and evolve experiments
- Remove dependencies between teams: architectural, communication, approval
- Think about system's architecture
- Leaders create alignment and transparency

### Encouraging Failure
- In a complex, adaptive system failure is inevitable
- When accidents happen, human factors are the starting point of a blameless post-mortem
- Ask: how can we get people better information?
- Ask: how can we detect and limit failure modes?
- Chaos monkey from netflix 
- Hold blameless post-mortems and retrospectives regularly after incidents
	
### Game Days

> For DiRT-style events to be successful, an organizaton first needs to accept system and process failures as a means of learning... We design tests that require engneers from several groups who might not normally work together to interact with each other. That way, should a real large-scale disaster ever strike, these people will already have strong working relationships"

- Fire drills for system
- Execute disasters and test recovery measures
- Disaster recovery testing brings teams together

### 5 Principles of Growth
- The job effective leadership is to create empowered, autonomous teams
- Leadership should create alignment and transparency so that those teams can move together in pursuit of the same organizational goals
- Managers should be helping teams to learn and improve
- Create psychological safety
- Practice failure in pursuit of learning
	
### Google re:WORK
- Psychological safety - Team members feel safe to take risks and be vulnerable in front of each-other
- Dependability - Team members get things done on time and meet Google's high bar for excellence.
- Structure & Clarity - Team members have clear roles, plans and goals
- Meaning - Work is personally important to team members
- Impact Team members think their work matters and creates change
