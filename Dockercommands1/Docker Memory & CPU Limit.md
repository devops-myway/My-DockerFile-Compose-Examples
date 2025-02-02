##### Introduction
- By default, Docker containers have unlimited access to the host's physical memory and CPU. Unless carefully controlled,
- a misbehaving container can consume more resources than planned and cause performance bottlenecks.

##### Understanding Docker Memory Limits
- memory limits improve security by preventing resource-based attacks.
- Docker allows users to define two types of memory limits:
- Hard limits: When a container exceeds a hard memory limit, Docker takes aggressive actions such as terminating the container.
- Hard limits: are usually applied for critical workloads that cannot afford occasional system instability.
- Soft Limits. When a soft limit is reached, Docker warns the user but does not take immediate action.
- Soft Limits: This type of limit allows occasional resource demand spikes, and administrators use them to set up monitoring and alert systems.

##### Risks of Running Out of Memory
- Allowing a Docker container to utilize all system memory resources can compromise the system's stability
- A misbehaving container may exhaust all the available memory and crash, creating an OOM (out-of-memory) event.
- OOM events have the following consequences:

- Disruption of critical processes.
- Sub-standard application performance.
- Problems with co-located containers.

##### How to Limit Docker Memory Usage
- Configuring the maximum amount of memory a container can use.
- Defining the amount of memory a container can swap to a disk.
- Setting the soft limit for the amount of memory assigned to a container.
##### Options:

- --memory=, -m	: Sets a hard limit to the maximum physical memory a container can utilize (with a minimum of 6 MB).
- --memory-swap=	: Allocates swap memory available to a container. Since its value represents the total available memory (RAM + swap), --memory-swap must be larger than --memory.
- --memory-swappiness=	: Sets the percentage of anonymous page swaps a container performs.
- --memory-reservation=	: Specifies a soft memory limit. If a hard limit is set for the same container, the soft limit must be smaller than the hard limit.
- --kernel-memory=	: Sets a hard limit to the maximum kernel memory for a container (with a minimum of 6 MB).
- --oom-kill-disable	: Disables killing the container processes when an OOM error occurs.

###### Set Maximum Memory Access 
- The value of memory-limit should be a positive integer followed by the suffix b, k, m, or g (short for bytes, kilobytes, megabytes, or gigabytes)
- to run an Nginx container and set its memory limit to 512 MB
``````sh
sudo docker run -dit --memory="[memory-limit]" [docker-image]
docker run -dit --memory="512m" nginx
``````
##### Set Swap to Disk Memory Limit
- Swap allows Docker to store data by writing directly to the disk after exhausting all RAM assigned to the container.
- When setting the swap limit, remember that the value includes the total amount of RAM and swap memory.
- If you do not want to use swap memory for a specific container, assign--memory and --memory-swap the same values.
``````sh
sudo docker info
sudo nano /etc/default/grub
GRUB_CMDLINE_LINUX="cdgroup_enable=memory swapaccount=1"
sudo update-grub

# The syntax for running a container with limited physical memory and additional swap memory is
docker run -dit --memory="[ram]" --memory-swap="[ram+swap]" [image]
docker run -dit --memory="512m" --memory-swap="1g" nginx

``````
##### Set Soft Limit to Container Memory
- The --memory option sets a hard limit that cannot be surpassed. To enforce a soft limit, use the --memory-reservation option
- A soft limit option warns when the container uses all assigned memory but does not stop the container's services.
- If both the --memory and --memory-reservation options are enabled, the soft limit must be lower than the hard limit.
``````sh
# create an Nginx container with a memory reservation of 512 MB and a hard memory limit of 1 GB:
docker run -dit --memory="1g" --memory-reservation="512m" nginx

``````
##### Verify Docker Memory Usage
- Confirm Docker enforces the hard memory limit by checking the container statistics:
- The limit is displayed in the MEM USAGE / LIMIT column.
``````sh
docker stats
# Check swap memory allocation with the docker inspect command
docker inspect [container-name] | grep MemorySwap
# Check swap memory allocation with the docker inspect command:
docker inspect [container-name] | grep MemorySwap
# Confirm the soft memory limit by looking for the MemoryReservation field in the output of the docker inspect
docker inspect [container-name] | grep MemoryReservation
``````
##### Limit Docker Container CPU Usage
- Docker containers do not have default limitations for using the host's CPU
- Option
- --cpus=	: Specifies a limit to the number of CPUs a container can use. The value can be a decimal number (e.g., 1.5).
- --cpu-period=	: Sets the CFS scheduler period. The default value is 100,000 microseconds. This option always goes in pair with --cpu-quota.
- --cpu-quota=	: Defines a CFS quota for a container, i.e., the time before the container is throttled, per CFS scheduler period. For example, setting --cpu-period to 100,000 and --cpu-quota to 150,000 is the same as setting the --cpus option to 1.5.
- --cpuset-cpus=	: Specifies the exact CPU units a container can use. The option takes the numeric value (a comma-separated list or a hyphen-separated range), where the first CPU is number 0, the second is number 1, etc.
- --cpu-shares=	: Allocates CPU cycles to a container. The default value is 1024. A smaller number allocates fewer shares, while a larger number allows more shares. This option sets a soft limit, i.e., the shares are enforced only when CPU cycles are constrained.

###### Limit Number of CPU Cores
- The --cpus option provides a way to limit how many CPU cores a container can use, value is a decimal number.
``````sh
docker run -dit --cpus="1.0" nginx

``````
###### Limit CPU Cycles
- When the system has sufficient CPU power, each container can use as much of it as needed.
- However, when CPU resources are low, use the --cpu-shares option to control the share of CPU cycles available to a container
- By default, this value is set to 1024.
``````sh
docker run -dit --cpu-shares="2048" nginx

``````
###### Verify Docker CPU Usage
- To check CPU limitations for a container, use the docker inspect commands below. For easier search, pipe the output to the grep command
``````sh
docker inspect [container-name] | grep NanoCpus
docker inspect [container-name] | grep CpuShares
``````
``````sh

``````
