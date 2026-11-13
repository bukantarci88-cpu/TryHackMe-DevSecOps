# DevSecOps 4.1. Intro to Containerisation

### Task 1 - Introduction

This room is the first of a series explaining the popular technology of containerisation.

### Task 2 - What is Containerisation

**Containerization** is the process of packaging an application and all its dependencies into a container, making it portable and easy to run across different environments. These dependencies can:

- Be difficult to install depending on the environment the application is running (some operating systems might not even support them!)
- Create difficulty for developers to diagnose and replicate faults, as it could be a problem with the application's environment - not the application itself!
- Can often conflict with each other. For example, having multiple versions of Python to run different applications is a headache for the user, and an application may work with one version of Python and not another.

Containerisation platforms remove this headache by packaging the dependencies together and “isolating” (**note:** this is not to be confused with "security isolation" in this context) the application’s environment. If the device supports the containerisation engine, a user will be able to run the application and have the same behaviours.

In the screenshot above, we can see how three applications and their environments (such as dependencies) are packaged together and do not directly interact with the physical computer - but rather the containerisation engine (in this case, it is Docker)

Containers use kernel namespaces to isolate processes and resources from each other, improving security and preventing compromised applications from affecting other containers. Unlike virtual machines or lab environments, containers do not require a full operating system, making them more efficient in terms of disk space, CPU, and memory usage.

**Answer the questions below**

> What is the name of the kernel feature that allows for processes to use resources of the Operating System without being able to interact with other processes?
> 

namespace

> In a normal configuration, can other containers interact with each other?
> 

nay

### Task 3 - Introducing Docker

**Docker** is an open-source containerization platform that allows applications to be packaged, deployed, and shared as **images**. These images can be easily downloaded and run on Linux, Windows, or macOS. Docker uses the **Docker Engine** to create and manage containers, providing isolated environments while efficiently accessing host resources such as CPU, memory, storage, and networking. Because of this, the Docker engine is extensive and allows you to do things like:

1. Connect containers together (for example, a container running a web application and another container running a database)
2. Export and import applications (images)
3. Transfer files between the operating system and container

Docker uses the programming syntax YAML to allow developers to instruct how a container should be built and what is run. This is a significant reason why Docker is so portable and easy to debug; share the instructions, and it will build and run the same on any device that supports the Docker Engine.

The Docker engine allows containers to be orchestrated, meaning that multiple containers can be built as part of a group, allowing containers to communicate with each other (for example, one container running a web server and another container running a database can communicate). We will come onto this feature in a later room.

**Answer the questions below**

> What does an application become when it is published using Docker?
> 

An Image

> What is the abbreviation of the programming syntax language that Docker uses?
> 

YAML

### Task 4 - The History of Docker

**Docker**, created by **Solomon Hykes** in 2013, began as an internal project at **dotCloud** before being released as open source. Although containerization concepts date back to **Unix V7** in 1979, Docker made containerization widely accessible and popular by simplifying application deployment and management. To be precise:

- 13 million developers are using Docker *[1]*
- There are 7 million applications made and ready to use with Docker *[2]*
- 13 billion applications are downloaded monthly! *[3]*
- …and this is just from the official repository

**Answer the questions below**

> In what year was Docker originally created?
> 

2013

> Where was Docker first showcased?
> 

PyCon

> What version of Unix had the first concepts of containerisation?
> 

V7

### Task 5 - The Benefits & Features of Docker

Docker is an agile, convenient and extensive means of deploying an application. 

**Docker is Free:** The Docker ecosystem is free to use and open-sourced. While business plans exist, you can completely download, use, create, run and share images.

**Docker is Compatible:** The Docker platform is compatible with Linux, macOS and Windows. Because of how containerisation works, if a device supports the Docker Engine, you can run any container, regardless of the application or dependencies.

**Docker is Efficient & Minimal:** Docker is an efficient way to isolate applications in comparison to alternatives such as lab machines. This is because the Docker Engine runs and interacts with the host operating system, and containers do not run a fully-fledged operating system for each container. For example, containers can share a minimal operating system image, meaning you only need to store it once.

A minimal Ubuntu image is 100MB~ which can be stored once and used multiple times. Compare this to the Ubuntu server image, about 1GB after a fresh install per VM.

Inspecting the size of the "ubuntu" docker image

```bash
ubuntu@thm:~$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       latest    27941809078c   4 weeks ago   77.8MB
ubuntu@thm:~$
```

**Docker is Easy to Get Started With:**  The Docker developer documentation is very well [documented](https://docs.docker.com/), with lots of articles, working examples and answered questions on the Internet. The chances are, if you want to do something in Docker, someone has already asked about or done it. The syntax to get started with Docker is easy to pick up. You can start your first container in no time (the fact that there are docker images for all sorts of applications already published helps.)

**Docker is Easy to Share With Others:** A significant benefit of Docker is its portability. Docker uses “images” to store instructions to dictate how the container should be built (just an instruction manual!). These “images” can be exported, shared and uploaded to both public and private repositories such as DockerHub and GitHub. The “image” can be run by anything that supports the Docker engine, as long as the syntax is valid.

**Docker is Minimal:** These Docker images discussed above are minimal. You will often find many-core and luxurious tools and packages in a container that are missing. While this can look like a disadvantage, it, in fact, allows:

- Containers to be built exactly how the developer wishes
- Better security, knowing exactly what runs within a container can reduce the risk of unnecessary packages becoming vulnerable and posing a security risk.

**Docker is Cheaper to Run:** Running containers is usually a cheaper option than running lab machines. This is especially noticeable in cloud environments, where CPU, RAM, and Disk space are expensive. For example, you can quite happily run a few containers on a single $5 cloud provider VPS, whereas you will not be able to run a lab machine. This is due to the fact that:

- Running lab machines requires hardware that supports virtualisation, which is only found on costly tiers of a cloud provider (if at all!)
- Lab machines require lots of memory and disk space, as you are running a separate operating system on top of the physical machine.

### Task 6 - How does Containerisation Work?

Namespaces essentially segregate system resources such as processes, files and memory away from other namespaces. Every process running on Linux will be assigned two things:

- A namespace
- A process identifier (PID)

Namespaces are how containerisation is achieved! Processes can only "see" other processes that are in the same namespace - no conflicts in theory. Take Docker, for example, every new container will be running as a new namespace, although the container may be running multiple applications (and in turn, processes). Let's prove the concept of containerisation by comparing the number of processes there are in a Docker container that is running a web server versus the host operating system at the time.

Put simply, the process with an ID of 0 is the process that is started when the system boots. Process numbers increment and must be started by another process, so naturally, the next process ID will be #1. This process is the systems `init` , for example, the latest versions of Ubuntu use `systemd`. Any other process that runs will be controlled by `systemd` (process #1).

We can use process #1's namespace on an operating system to escalate our privileges. Whilst containers are designed to use these namespaces to isolate from each other, they can instead coincide with the host computer's processes.

**Answer the questions below**

> What command can we use to view a list of running processes?
> 

ps aux  

### Task 7 - Practical

**Deploy** the static site attached to this task. Containerise the applications to reveal the flag!

**Answer the questions below**

> Containerise the applications in the static site. What is the flag?
> 

THM{APPLICATION_SHIPPED}

<img width="604" height="560" alt="image" src="https://github.com/user-attachments/assets/bb5cd474-24ca-49e9-afb0-3286d28ffe13" />
