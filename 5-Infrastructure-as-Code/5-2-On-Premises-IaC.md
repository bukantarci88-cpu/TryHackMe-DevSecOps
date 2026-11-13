# DevSecOps 5.2. On Premises IaC

### Task 1 - Introduction

On-premises infrastructure as code remains vital for heavily regulated industries requiring maximum compliance, data sovereignty, and direct physical control over deployment environments.

### Task 2 - Why On-Premises

On-premises IaC automates the deployment and configuration of systems and services residing directly within an organization's private, locally owned data center.

**Reasons for On-Prem IaC**

- **Complete Pipeline Control:** On-premises IaC gives organizations full sovereignty over both the software stack and underlying hardware, permitting aggressive network isolation (e.g., VPN-only access) that eliminates public internet exposure.
- **Third-Party Risk Mitigation:** Operating private infrastructure removes reliance on external SaaS providers—such as cloud platforms or hosted code repositories—eliminating supply chain and third-party infrastructure vulnerabilities.
- **Tailored Sector Compliance:** Direct control allows organizations in highly regulated environments (like finance and government) to enforce custom security baselines and satisfy strict data isolation requirements that third-party multi-tenant environments cannot guarantee.

**On-prem IaC has the following benefits:**

- Allows full control of the IaC pipeline
- The IaC pipeline can be fully customised for your exact needs
- Data protection and regulation is easier to perform as the IaC pipeline does not host resources on third-party systems
- While the initial investment cost of the infrastructure is high, there are no monthly fees for hosting the IaC pipeline
- It is often easier to do small deployments

**On-prem IaC has the following drawbacks:**

- As you are hosting your own IaC pipeline, you are fully responsible for its security and configuration
- Scalability of deployments is not as flexible as cloud-based IaC, which can lead to either not having sufficient resources for the deployment or an over-investment in resources
- The initial investment for on-prem IaC is significantly higher than cloud-based IaC

**Answer the questions below**

> If I want more control over my pipeline, should I use an on-prem IaC pipeline?
> 

Yea

> If I want to have a flexible and easily scalable deployment, should I use an on-prem IaC pipeline?
> 

Nay

> If I have strict data protection regulations enforced on my organisation, should I use an on-prem IaC pipeline?
> 

Yea

### Task 3 - Vagrant Basics

Vagrant is a software solution that can be used for building and maintaining portable virtual software development environments. In essence, Vagrant can be used to create resources from an IaC pipeline. You can think of Vagrant as the big brother of Docker. In the context of Vagrant, Docker would be seen as a provider, meaning that Vagrant could be used to not only deploy Docker instances but also the actual servers that would host them.

**Terminology**

| **Term** | **Definition** |
| --- | --- |
| Provider | A Vagrant provider is the virtualisation technology that will be used to provision the IaC deployment. Vagrant can use different providers such as Docker, VirtualBox, VMware, and even AWS for cloud-based deployments. |
| Provision | Provision is the term used to perform an action using Vagrant. This can be actions such as adding new files or running a script to configure the host created with Vagrant. |
| Configure | Configure is used to perform configuration changes using Vagrant. This can be changed by adding a network interface to a host or changing its hostname. |
| Variable | A variable stores some value that will be used in the Vagrant deployment script. |
| Box | The Box refers to the image that will be provisioned by Vagrant. |
| Vagrantfile | The Vagrantfile is the provisioning file that will be read and executed by Vagrant. |

**Vagrant Example:** In our example here, we have the following folder structure:

```bash
.
├── provision
│   ├── files.zip
│   └── script.sh
└── Vagrantfile
```

The Vagrantfile script has the following code:

```c
Vagrant.configure("2") do |cfg|
  cfg.vm.define "server" do |config|
    config.vm.box = "ubuntu/bionic64"
    config.vm.hostname = "testserver"
    config.vm.provider :virtualbox do |v, override|
       v.gui = false
       v.cpus = 1
       v.memory = 4096
    end
```

In this example, we are going to provision two servers. Both of these servers will start with the base Ubuntu Bionic x64 image. Like Docker, these images will be pulled from a public image repo. We could, however, tell Vagrant where to find these images, meaning they can be pulled from a private repo of images as well. You can see that both servers are configured with a hostname and how many CPUs (1) and RAM (4GB) each host will have. The first server will also have two network interfaces with static IPs attached. The second server will have a file called `files.zip` uploaded, and a script called `script.sh` will be executed on the host. If we want to provision the entire script, we would use the command `vagrant up`. This would provision both servers in the order specified in the Vagrantfile. We could also decide to only provision one of the servers by using the server name. For example, `testserver` could be provisioned using `vagrant up server`.

**Answer the questions below**

> What is the name of the file that Vagrant uses for provisioning?
> 

Vagrantfile

> What command can be used to provision all hosts with Vagrant?
> 

vagrant up

> What command can be used to provision only the webserver host with Vagrant?
> 

vagrant up webserver

### Task 4 - Ansible Basics

The next IaC tool to learn about is [Ansible](https://www.ansible.com/). Like Vagrant, Ansible is another suite of software tools that allows you to perform IaC. Ansible is also open-source, making it a popular choice for IaC pipelines and deployments. One main difference between Ansible and Vagrant is that Ansible performs version control on the steps executed. This means that Ansible is similar to Docker, in that it will only perform updates on steps that require updates, instead of requiring a full reprovision.

**Terminology**

| **Term** | **Definition** |
| --- | --- |
| Playbook | An Ansible playbook is a YAML file with a series of steps that will be executed. |
| Template | Ansible allows for the creation of template files. These act as your base files, like a configuration file, with placeholders for Ansible variables, which will then be injected into at runtime to create a final file that can be deployed to the host. Using Ansible variables means that you can change the value of the variable in a single location and it will then propagate through to all placeholders in your configuration. |
| Role | Ansible allows for the creation of a collection of templates and instructions that are then called roles. A host that will be provisioned can then be assigned one or more of these roles, executing the entire template for the host. This allows you to reuse the role definition with a single line of configuration where you specify that the role must be provisioned on a host. |
| Variable | A variable stores some value that will be used in the Ansible deployment script. Ansible can take this a step further by having variable files where each file has different values for the same variables, and the decision is then made at runtime for which variable file will be used. |

**Ansible Example:** Ansible makes use of a specific folder and file structure. The most important part of the structure is the playbook, a YAML file that ultimately dictates what commands will be executed for provisioning. Let's take a look at the typical folder structure:

```bash
.
├── playbook.yml
├── roles
│   ├── common
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   ├── apt.yml
│   │   │   ├── main.yml
│   │   │   ├── task1.yml
│   │   │   ├── task2.yml
│   │   │   └── yum.yml
│   │   ├── templates
│   │   │   ├── template1
│   │   │   └── template2
│   │   └── vars
│   │       ├── Debian.yml
│   │       └── RedHat.yml
│   ├── role2
│   ├── role3
│   └── role4
└── variables
    └── var.yml
```

The folder structure was only expanded for the `common` role. However, this same structure would apply for roles 2 to 3. Let's dive a little bit into what each of these files would do. Let's start with the playbook.yml file:

```c
---
- name: Configure the server
  hosts: all
  become: yes
  roles:
    - common
    - role3
  vars_files:
    - variables/var.yml
```

As we can see, the playbook specifies that the `common` and `role3` roles should be applied for all hosts where this Ansible playbook will be executed. It will also use the `var.yml` file to overwrite any default variables within the roles. This allows us to only change the default variables where needed, as the defaults will then still be applied to all other variables.

To execute the `common` role, Ansible will load variables from the `defaults/main.yml` file and overwrite these variables with any new values found in the `var.yml` file. It will then read and execute the `tasks/main.yml` file. This file would look something like this:

```c
---
- name: include OS specific variables
  include_vars: "{{ item }}"
  with_first_found:
    - "{{ ansible_distribution }}.yml"
    - "{{ ansible_os_family }}.yml"

- name: set root password
  user:
    name: root
    password: "{{ root_password }}"
  when: root_password is defined

- include: apt.yml
  when: ansible_os_family == "Debian"

- include: yum.yml
  when: ansible_os_family == "RedHat"

- include: task1.yml
- include: task2.yml
```

The first section of the Ansible file will determine the OS distribution of the host. It will then use this information to include OS-specific provisioning steps. As you can see from our folder structure, we have specific provisioning steps based on whether the distribution is Debian or RedHat. The second section of the playbook will set the root user's password. In the third section, we update the host and install some packages. However, we only execute the installing step matching the OS distribution. If the host is Debian, we will execute the commands specified in the `apt.yml` file. If the host is RedHat, we will execute the commands specified in the `yum.yml` file. This allows our Ansible role to be OS distribution agnostic. Lastly, we will execute the commands contained within the `task1.yml` and `task2.yml` files. These can be any commands, such as adding files to the host or making configuration changes.

**Combining Vagrant and Ansible:** While you could stick to one IaC tool for your entire pipeline, it can often be beneficial to combine toolings. For example, Vagrant could be used for the main deployment of hosts, and Ansible can then be used for host-specific configuration. This way, you only use Vagrant when you want to recreate the entire network from scratch but can still use Ansible to make host-specific configuration changes until a full rebuild is required. Ansible would then run locally on each host to perform these configuration changes, while Vagrant will be executed from the hypervisor itself. In order to do this, you could add the following to your Vagrantfile to tell Vagrant to provision an Ansible playbook:

```c
config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "provision/playbook.yml"
    ansible.become = true
end
```

It is also worth noting some of the differences between Vagrant and Ansible, if you are looking to only stick to a single IaC solution:

| **Feature/Aspect** | **Vagrant** | **Ansible** |
| --- | --- | --- |
| **Configuration Language** | Ruby (for Vagrantfiles). | YAML (for Playbooks). |
| **Integration with Other Tools** | Often used with provisioning tools like Chef, Puppet, or Ansible. | Can be used standalone or integrated with other CI/CD tools. |
| **Complexity** | Relatively straightforward for setting up development environments. | Higher complexity for larger infrastructures and advanced configurations. |
| **Scalability** | More suited for smaller-scale, individual development environments. | Highly scalable, suitable for managing complex, multi-tier applications. |
| **Execution Model** | Procedural style with sequential execution steps. | Declarative model, describing the desired state of the system. |

**Answer the questions below**

> What is the name given to the file that Ansible will provision?
> 

Playbook

> What is the name given to an Ansible file that will be updated, through the injection of Ansible variables, to create a final file during the provisioning process?
> 

Template

> What is the name given to an Ansible collection that can be provisioned with a single configuration request?
> 

Role

### Task 5 - Building an On-Prem IaC Workflow

**Creating an IaC Pipeline:** Now that we have learned the basics about on-prem IaC, it is time to put our knowledge to the test! Once the machine has started, you can navigate to the `/home/ubuntu/iac/` directory. All of the scripts that we will be using today can be found in this directory.

Got private key: 

```jsx
ubuntu@tryhackme:~/iac$ cd keys
ubuntu@tryhackme:~/iac/keys$ ls 
id_rsa  id_rsa.pub
ubuntu@tryhackme:~/iac/keys$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/t0gKwi2a9WsrvpW0+8/FwWGozSaXX8S2Qngw6Is+qWJjzYlcZ2g9P+0dEVtS5oNrEZ0O/JiNaELKWRkWbhN6GUWT9J3JanbuJtW7pMWO0jU30Fgg+ulsP5ui6V+DqUjFpvtN6WhMB7hlGsg6ughJx4vNN9OropIQME2eY5qVGt50BYZN+KrjrmcqCh+8yEpywSrHyDvQWz+OZ71qa0Tb1DFPR5RGUW2Siyp4HOr5lEvqgGWj4uMyrklN088XoNk2YOgbFiSpYHgFrmAYcJaT5BCp6kcoNQfhnqN1SpbSrJ/5YRlUo/WZGqq3rDBI1pA2hHI2G8CD5Zlg7076JELf0K/nnawfQfzzo2QRnah+q3kU+cjO5eYilFbvjPU05AWH2nbwvYVMeW/qv7x/2tfED6B5o9CKE13l0Kbx8IObjnsRb4HkPpIYxfMEbJVDhLWggkfuk3dFRlXTEqUiqbWh8KiUfC9CHox7TXjMcUvwNcBSxM+dV2QHc6Pt0HRniok= ubuntu@tryhackme
```

Before: 

```jsx
ubuntu@tryhackme:~/iac$ ls
Vagrantfile keys provision
ubuntu@tryhackme:~/iac$ vagrant up
Bringing machine 'dbserver' up with 'docker' provider...
```

**Vagrantfile:** Let's start by taking a look at the Vagrantfile:

```python
Vagrant.configure("2") do |config|
  # DB server will be the backend for our website
  config.vm.define "dbserver"  do |cfg|
    # Configure the local network for the server
    cfg.vm.network :private_network, type: "dhcp", docker_network__internal: true
    cfg.vm.network :private_network, ip: "172.20.128.3", netmask: "24"

    # Boot the Docker container and run Ansible
    cfg.vm.provider "docker" do |d|
      d.image = "mysql"
      d.env = {
        "MYSQL_ROOT_PASSWORD" => "mysecretpasswd"
      }
    end
  end
```

**DB Server:** The first machine that will be provisioned is the `dbserver`. Working through the lines of code, we can see that the machine will be added to a local network and receive the IP of `172.20.128.3`. We can also see that the provision directory will be mounted as a share. Lastly, using Docker as the provider, the `mysql` image will be booted and the mysql password will be configured to be `mysecretpasswd`.

**Web Server:** The second machine that will be provisioned is the `webserver`. Similar to the `dbserver` machine, it will be connected to the network and use Docker as its provider. However, there are some slight differences. Firstly, the webserver will expose SSH. Since we are using Docker, we have to alter some of the default Vagrant configurations to allow Vagrant to connect via SSH. This includes changing the username and the private key that will be used for the connection. Secondly, we can see that an Ansible playbook will be executed on the container by looking at the following line:

```c
cfg.vm.provision "shell", inline: "ansible-playbook /vagrant/provision/web-playbook.yml"
```

**Ansible Playbook:** Let's start by reviewing the `web-playbook.yml` file:

```c
- hosts: localhost
  connection: all
  roles:
    - webapp
```

This is a simple Ansible script that indicates that the webapp role will be provisioned on the host. To better understand what the webapp role will entail, we can start by reviewing the `~/iac/provision/roles/webapp/tasks/main.yaml` file:

```c
- include_tasks: "db-setup.yml"
- include_tasks: "app-setup.yml"
```

This shows us that there will be two main portions to the Ansible provisioning. At this point, it is worth taking a look as well at the default values in the `~/iac/provision/roles/webapp/defaults/main.yml` file:

```c
db_name: BucketList
db_user: root
db_password: mysecretpasswd
db_host: 172.20.128.3

api_key: superapikey
```

**DB Setup:** Let's take a look at the `db-setup.yml` file:

```c
- name: Create temp folder for SQL scripts
  ansible.builtin.file:
    path: /tmp/sql state: directory

- name: Time delay to allow SQL server to boot
    shell: sleep 10
```

From the script, we can see that 7 tasks will be performed. Reading through these tasks, we can see that a temporary folder will be created where SQL scripts will be pushed to and then executed against the database host. Let's take a look at how Ansible would inject those variables from before. Take a look at the `Create DB` task's shell command:

```bash
shell: mysql -u {{ db_user }} -p{{ db_password }} -h {{ db_host }} < /tmp/sql/createdb.sql
```

As you can see, the three variables of `db_user`, `db_password`, and `db_host` will be injected using either the values for the default file, or the overwritten values, if they exist. Ansible allows us to take this a step further. Let's take a look at the actual `createdb.sql` file:

```sql
drop DATABASE IF EXISTS {{ db_name }};

CREATE DATABASE {{ db_name }};
USE {{ db_name }};

drop TABLE IF EXISTS 'tbl_user';

CREATE TABLE {{ db_name }}.tbl_user (
  'user_id' BIGINT AUTO_INCREMENT,
  'user_name' VARCHAR(45) NULL,
  'user_username' VARCHAR(45) NULL,
  'user_password' VARCHAR(162) NULL,
  PRIMARY KEY ('user_id'));
```

As we can see, these variables are even injected into the file templates that will be used. This allows us to control the variables that will be used from a single, centralised location. When we change the user or password that will be used to connect to the database, we can change this in a single location, and it will propagate throughout all provisioning steps for the role.

**Web Setup:** Lastly, let's take a look at the `app-setup.yml` file:

```c
- name: Copy web application files
  shell: cp -r /vagrant/provision/roles/webapp/templates/app /

- name: Copy Flask app script with injected variables
  template:
    src: templates/app.py
    dest: /app/app.py
```

This file only has two tasks. The first copies the artefacts required for the web application and the second copies the web application file as a template. A template copy is performed to ensure that the variables, such as the database connection string, are injected into the script as well. 

**Running the IaC Pipeline:** Now that we have an understanding of our pipeline, it is time to start it! Let's start our pipeline and the provisioning using `vagrant up` from the `iac` directory. The pipeline will take a while to boot, but pay attention to what is happening.

**Note:** While you may see some red on the terminal when the Ansible provisioning step is running, as long as these lines only indicate warnings and not an error, the provisioning will complete as expected.

Once our pipeline has provisioned the machines, we can verify that they are running using the `docker ps` command:

```bash
ubuntu@tryhackme:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                    NAMES
04fc30613dd6   mysql     "docker-entrypoint.s…"   18 minutes ago   Up 18 minutes   3306/tcp, 33060/tcp      iac_dbserver_1706019401
5529f04a2509   ansible   "/usr/sbin/sshd -D"      18 minutes ago   Up 18 minutes   127.0.0.1:2222->22/tcp   iac_webserver_1706019400
```

If this is running, we can start our web application using the following command:

`vagrant docker-exec -it webserver -- python3 /app/app.py`

Once loaded, you can navigate to the web application using the lab machines's browser: 

![](https://cdn-images.tryhackme.com/user-uploads/6093e17fa004d20049b6933e/room-content/39e697edab7cba60952ccdb28ece98c9.png)

**Answer the questions below**

> What is the flag displayed on the web application that is hosted after your IaC pipeline provisioning? You will need to create a profile and authenticate to view the flag.
> 

THM{IaC.Pipelines.Can.Be.Fun}

Got the contents of an SSH public key (`id_rsa.pub`) and an SSH private key (`id_rsa`) in the terminal

<img width="660" height="596" alt="image" src="https://github.com/user-attachments/assets/0a0a395f-e53f-4ef1-875b-660a71b6c889" />


Started the Vagrant environment, creating and provisioning the virtual machines defined in the project configuration.

```
ubuntu@tryhackme:~/iac/keys$ vagrant up
```

Executed an interactive command inside the `webserver` Docker container using Vagrant to run a Python Flask web application script. 

```
vagrant docker-exec -it webserver -- python3 /app/app.py
```

Listed the active Docker containers and started a Python Flask web application inside a container via Vagrant, viewing its runtime access logs and a traceback exception.   

<img width="660" height="596" alt="image 1" src="https://github.com/user-attachments/assets/b6b896b7-0a37-46c7-a9c9-9c91731e29d6" />


Sign up then sign in then get flag: 

<img width="719" height="616" alt="image 2" src="https://github.com/user-attachments/assets/894af599-6307-46b4-9137-58731d1d2687" />


### Task 6 - Security Concerns in On-Prem IaC

Securing on-premises IaC requires moving beyond high-level compliance frameworks to actively harden the pipeline against practical attack vectors across state security, secrets handling, supply chain integrity, and execution privileges.

**Dependencies:** Unpatched security flaws or vulnerabilities present in base images and third-party dependencies will automatically propagate throughout the entire infrastructure generated by the IaC pipeline. 

Outdated operating system images leave deployed hosts exposed, underscoring the critical need for proactive dependency management whenever third-party software is integrated into an IaC pipeline.

**Defaults:** Hardcoded default credentials used by automation tools during host provisioning create significant security misconfigurations if they are not systematically changed or removed as the final deployment step. Retaining Vagrant's built-in `vagrant:vagrant` default credentials on provisioned Windows hosts provides an unauthenticated attack vector for adversaries to gain full administrative control over the entire environment.  Deploying application roles with default credentials (such as Jenkins using `jenkins:jenkins`) introduces severe vulnerabilities if end-users fail to perform initial password changes or if unused services remain unconfigured. IaC pipeline design must explicitly account for all default credentials, ensuring their remediation is either automated into the final execution steps or mandated for immediate post-deployment manual cleanup.

**Insufficient Hardening:** Because IaC prioritizes deployment speed, target environments often remain unhardened unless specific hardening steps are intentionally integrated into the pipeline or executed manually post-deployment. Management protocols required during provisioning—such as WinRM for Windows host setup—must be explicitly disabled after deployment if not needed for routine operations to prevent lateral movement by threat actors.

**Remote Code Execution as a Feature:** IaC pipelines function as authorized remote code execution systems, requiring robust access controls to prevent unauthorized users from leveraging the pipeline to compromise connected networks. Protecting IaC pipelines against total compromise requires implementing centralized secrets management to prevent credential leaks in source code alongside strict least-privilege access controls for pipeline users and services. Securing IaC pipelines against real-world threats forms the foundational baseline required before attempting to evaluate, audit, or attack vulnerable IaC execution workflows.

### Task 7 - Attacking On-Prem IaC

Before starting to apply the SSH connection to the entry account. 

```
root@ip-10-114-64-201:~# ssh [entry@10.114.176.196](mailto:entry@10.114.176.196)
```

**IaC Challenge:** Now that you have learned how on-prem IaC deployments work and the security concerns that arise when using IaC, it is time to put your knowledge to the test. Start the machine attached to this task by pressing the green **Start Lab Machine** button and wait for the machine to boot. Once authenticated, you will find an IaC pipeline's scripts. Work through these files to identify vulnerabilities and attack the machines deployed by the IaC pipeline to ultimately gain full control of the pipeline! You can also use this SSH connection to "catch shells" as required. You will have to leverage these files together with what you learned in Task 5 to be able to compromise the pipeline!

To assist you on this journey, you can make use of the hints provided below. However, since the main goal is attacking an IaC pipeline, you are provided with the following:

- [Nmap](https://tryhackme.com/module/nmap) has been installed for you on the host, allowing you to scan the port range of the Docker network, if required.
- Use [SSH to proxy](https://tryhackme.com/room/lateralmovementandpivoting) out the traffic of the web application, or any other port, as required.
- You can use the SCP command of SSH to transfer out the IaC configuration files.

**Answer the questions below**

> What is the value stored in the flag1-of-4.txt file?
> 

THM{Dev.Bypasses.and.Checks.can.be.Dangerous}

After appling the ssh connection, navigated into the `iac` directory, listed its files (`Vagrantfile` and `provision`), and opened the `Vagrantfile` using the Vim text editor.  

<img width="728" height="304" alt="image 3" src="https://github.com/user-attachments/assets/953cc194-2927-4005-b41d-d394354c849d" />


Viewed the `dbserver` configuration in the `Vagrantfile`, which sets up a Docker container using the `mysql_vuln` image, assigns it a static IP, and configures the environment variable `MYSQL_ROOT_PASSWORD` as `mysecretpasswd`. 

<img width="728" height="452" alt="image 4" src="https://github.com/user-attachments/assets/61ce46a6-3e61-4140-ba3d-26f05a5409b1" />


Checked ip s in which domain we are.

```
root@ip-10-114-64-201:~# ip ad
```

<img width="722" height="130" alt="image 5" src="https://github.com/user-attachments/assets/f659c1bb-286e-4340-b171-3e6d60f1b299" />


Applied nmap -F 172.17.0.1 to see open ports

```
$ nmap -F 172.17.0.2
Starting Nmap 7.80 ( [https://nmap.org](https://nmap.org/) ) at 2026-11-05 08:55 UTC
Nmap scan report for ip-172-17-0-2.eu-central-1.compute.internal (172.17.0.2)
Host is up (0.00016s latency).
Not shown: 98 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
3306/tcp open  mysql
```

```
Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
$ nmap -F 172.17.0.3
Starting Nmap 7.80 ( [https://nmap.org](https://nmap.org/) ) at 2026-11-05 08:56 UTC
Nmap scan report for ip-172-17-0-3.eu-central-1.compute.internal (172.17.0.3)
Host is up (0.00012s latency).
Not shown: 98 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

Applied curl command: 

```
$ curl [http://172.17.0.](http://172.17.0.2/)3 -i
```

<img width="722" height="247" alt="image 6" src="https://github.com/user-attachments/assets/1b3c4c5d-3944-4a73-af9d-33ea3f892f14" />

<img width="1172" height="393" alt="image 7" src="https://github.com/user-attachments/assets/661d00fa-f349-4ed4-94c1-accc0ba5ab2b" />


After sign up go to page inspector, inserted command below, then refresh page

<input type="hidden" name="commend" value="cat flag-of-4.txt">

Attempted to initiate an SSH connection to IP `172.20.128.3` as the `root` user using the private key file `key1`

```
ls
iac  key2 ssh -i key1 [root@172.20.128.3](mailto:root@172.20.128.3)
```

Logged into a Debian system as `root`, inspected home directory files including `flag1-of-4.txt`, checked directory contents in `/tmp/provision`, and navigated to `/vagrant/keys` to find SSH key files (`id_rsa` and `id_rsa.pub`). 


<img width="485" height="58" alt="image 8" src="https://github.com/user-attachments/assets/4dd6b51d-12ac-4ac8-ae98-13190123b990" />

<img width="485" height="280" alt="image 9" src="https://github.com/user-attachments/assets/932882a8-5804-44cd-bde8-b86433f05a57" />

<img width="485" height="199" alt="image 10" src="https://github.com/user-attachments/assets/3bc02118-debc-40a4-a385-419eb123295f" />


> What is the value stored in the flag2-of-4.txt file?
> 

THM{IaC.Deployment.Keys.Must.be.Removed}

Connected via SSH to `172.20.128.2` as `root` using the key file `key1`, listed the home directory, and read the contents of `flag2-of-4.txt` (`THM{IaC.Deployment.Keys.Must.be.Removed}`).  

<img width="485" height="257" alt="image 11" src="https://github.com/user-attachments/assets/b5906937-1047-478a-bc0d-3dc3e45fadd0" />


> What is the value stored in the flag3-of-4.txt file?
> 

THM{IaC.Shares.Should.be.Restricted}

Listed the contents of `/tmp`, navigated into `/tmp/datacopy`, and listed its contents, discovering standard user directories along with `flag3-of-4.txt`, application archives (`flask-app-terminator.zip`, `flask-webapp.zip`), and setup scripts (`script.sh`, `script_start.sh`).  

<img width="485" height="283" alt="image 12" src="https://github.com/user-attachments/assets/dc5dba4a-d474-4df8-90d9-73d02b228e6c" />

<img width="485" height="46" alt="image 13" src="https://github.com/user-attachments/assets/cd0b490c-e114-4da2-98d1-7eef3c3be452" />


> What is the value stored in the flag4-of-4.txt file?
> 

THM{Provisioners.Usually.Have.Privileged.Access}

<img width="485" height="94" alt="image 14" src="https://github.com/user-attachments/assets/415b7e96-2926-4181-9089-a47774fc3ee4" />


Created a private key file, restricted its permissions using `chmod 400 key`, SSHed into `ubuntu@10.10.198.5`, escalated to root using `sudo su`, and read the final flag `flag4-of-4.txt`

<img width="328" height="58" alt="image 15" src="https://github.com/user-attachments/assets/2e4c9def-0c80-494b-b0da-eb925ca2dfad" />

<img width="471" height="192" alt="image 16" src="https://github.com/user-attachments/assets/e5c03307-4eff-4978-8b14-3f1025d6a092" />


### Task 8 - Conclusion

In this room, we learned about on-prem IaC.  

- There are still reasons today why IaC could be performed on-prem instead of in the cloud
- Vagrant and Ansible are two sets of software that can be used for IaC pipelines
- With IaC pipelines, we have even larger security concerns as the IaC pipeline itself is remote code execution that a threat actor could leverage to create new machines
- While IaC pipelines provide automation for the deployment of infrastructure, we are still responsible for manually, or through the use of the automation, harden the deployed infrastructure
