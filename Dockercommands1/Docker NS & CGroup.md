###### To begin with, isolation
- Isolation is understood as a way of packaging an application with all the resources necessary for its execution and,
- in this way, the software will always behave in the same way, regardless of the environment (development, staging and production).
##### What is Docker
- Docker is responsible for managing several processes, or services, in isolation from the host,
- such as a database and an application, for example, running in different containers.
##### Container, Namespaces and Cgroups
- Container separation, or isolation, is possible because Docker uses two Linux Kernel features: Namespaces and Cgroups.

##### Namespaces: 
- are responsible for generating the isolation of groups of processes at their logical level, such as user management, network, etc.,
- ensuring that the container does not see the host processes and vice versa.
- Therefore, when creating a container, namespaces are created such as pid (Process ID) to isolate processes,
- net (Network) to control and isolate the networks of each container, ipc (Inter Process Communication) that allows communication between processes, etc.
##### Cgroups
- To manage the physical resources that are shared between these processes, there is Cgroups
- It gives Docker the power to share CPU, memory, I/O, etc., between the host and the container
- it is possible to limit or restrict these resources to specific containers.
``````sh


``````
