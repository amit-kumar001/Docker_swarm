# Docker_swarm
<ol>
A <strong>swarm</strong> is a group of machines that are running Docker and joined into a cluster </br>
swarm turns multiple docker hosts into a single, virtual docker host.</br>
Docker Swarm is a tool for Container Orchestration</br></br>

Let’s take an example</br>
You have 100 containers</br>
You need to do </br>
- Health check on every container</br>
- Ensure all containers are up on every system</br>
- Scaling the containers up or down depending on the load</br>
- Adding updates/changes to all the containers</br></br>

<strong>Orchestration</strong> - managing and controlling multiple docker containers as a single service.

----------------------------------------------------------------------------------------
## Requirements
<ol>
<li>Docker 1.13 or higher</li>
 1
<li>Docker Machine (pre installed for Docker for Windows and Docker for Mac)</li>
</ol>  

----------------------------------------------------------------------------------------

On <strong>mac OS</strong> and <strong>Windows</strong>, Machine is installed along with other Docker products when you install the Docker for Mac, Docker for Windows, or Docker Toolbox.</br>
But in ubuntu you need to install the machine</br>
Command to install docker-machine in ubuntu</br>
<ol>
<li><strong>base=https://github.com/docker/machine/releases/download/v0.16.0 &&</br>
curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&</br>
sudo install /tmp/docker-machine /usr/local/bin/docker-machine</strong></li>
  2
<li>Command to check docker-machine version</li>
<strong>$docker-machine version</strong></br>
<strong>$docker-machine -v</strong></br>
</ol>
------------------------------------------------------------------------------
<li>Step 1 : create docker machine to act as node for docker swaarm)</li>
Create one machine as manager and other as workers.</br>
<ol>
<li>To check all docker-machine commands.</li>
<strong>$ docker-machine</strong> 

<li>Command to create docker machine node.</li>
<strong>$ docker-machine create --driver virtualbox manager1</strong></br>
docker-machine:Error with pre-create check: “exit status 126”</br>
<strong>Error with pre-create check: "VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path"</strong></br>


<li>Need driver to create machine: we going to use virtualbox</li>
<strong>$ sudo apt-get install virtualbox</strong></br>
3

<li>Now create docker machine manager </li>
<strong>$ docker-machine create --driver virtualbox manager1</strong></br>
4

<li>To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run:</li>
<strong>$ docker-machine env manager1</strong></br>
5
</ol>
------------------------------------------------------------------------------</br>
<li>Step 2: Check machine created successfully</li>
<ol>
<li>docker-machine ls to list out the machines that we have</li>
<strong>$ docker-machine ls</strong></br>
6

<li>The machine has been created, it's active the driver is VirtualBox.</li>

<li>IP of this particular machine</li>
<strong>$ docker-machine ip manager1</strong></br>

<li>Now we need to create more machine with same command but change the name of the machine.</li>
<strong> $ docker-machine create --driver virtualbox worker1.1</strong></br>
 <strong>$ docker-machine create --driver virtualbox worker1.2</strong></br>
In short we have 3 nodes out of which one will be manager 2 will be workers.</br>
7
</ol>
----------------------------------------------------------------------------------</br>
<li>Step 3: <strong>SSH</strong>(connect) to docker machine.</li>
<ol>
Open new terminal (ctrl+shift+t)</br>
<li>Log into or run a command on a machine using SSH.</li>
To login, just run docker-machine ssh machinename:</br>
We will look how to do ssh into a docker machine directly and to use it as a <strong>VM machine.</strong></br>
9
<li>Every time we need to open new terminal to link with ssh</li>
<strong>$ docker-machine ssh manager1</strong></br>
<strong>$ docker-machine ssh worker1.1</strong></br>
<strong>$ docker-machine ssh worker1.2</strong></br>

<strong>SSH key</strong>
You can link your Cloud and Service providers so that Docker Cloud can provision and manage swarms on your behalf. For this, you need an SSH key to authenticate Docker to your provider.</br>
SSH is a secure protocol for accessing remote machines and applications. It provides authentication and encrypts data communication over insecure networks.</br>
</ol>
---------------------------------------------------------------------------</br>
<li>Step 4: Initialize Docker Swarm.</li>
<ol>
<li>Check node_id</li>
<strong>$ docker-machine ip manager1</strong></br>

<li>Now use same terminal in which we add a link between manager1 and ssh key.</li>
<strong>$ docker swarm init --advertise-addr MANAGER_IP</strong></br>
<strong>$ docker swarm init --advertise-addr 192.168.99.100</strong></br>
10
We can add more manager with same command.</br>
<li>Check the node status</li>
This command is run in only manager terminal(this command will work only in swarm manager and not in worker).</br>
<strong>$ docker node ls</strong></br>
11
</ol>
-------------------------------------------------------------------------------</br>
<li>Step 5: Join workers in the swarm.</li>
<ol>
<li>Command for joining as worker.</li>
Run command in manager1 node(terminal). </br>
This will give command to join swarm as worker</br>
<strong>$ docker swarm join-token worker</strong>
12
<li>Copy the swarn join command and run in worker-machine terminal to add worker for the manager node</li>
<strong>$ docker swarm join --token SWMTKN-1-1627nzpuotunfseghhlp0l8f5osvikkyi5cqmqezspoq2n1w8b-39fj8930ndg14mq20oy1ey86m 192.168.99.100:2377</strong></br>
13

<li>Now go back in to manager terminal and check the status of all nodes</li>
<strong>$ docker node ls</strong></br>
14


<li>Another way to join manager</li>
docker swarm join-token manager</br>
This will give command to join swarm as manager.</br>
<strong>docker swarm join --token SWMTKN-1-1627nzpuotunfseghhlp0l8f5osvikkyi5cqmqezspoq2n1w8b-1fv7a9blk474i605hbwz7vt6l 192.168.99.100:2377</strong></br>
15
SSH into worker node(machine) and run command to join swarm as worker.</br>
In Manager Run command - docker node ls to verify worker is registered and is ready.</br>
Do this for all worker machines.</br>
</ol>
-----------------------------------------------------------------------------------</br>
<li>Step 6: On manager run standard docker commands.</li>
<ol>
<li>Check swarm section in which all details about swarm no of manager, nodes(manager is also node) etc.</li>
<strong>$ docker info</strong>
16

<li>Now check docker swarm command options.</li> 
<strong>$ docker swarm </strong>
17
</ol>
----------------------------------------------------------------------------------</br>
<li>Step 7: Run containers on Docker Swarm.</li>
<ol>
<li>Now we will create a services, and run these services to create replicas with count(number) of replica.</li> 
Run this command in manager1 terminal.</br>
docker service create --replicas 3 -p 80:80 --name service_Name nginx</br>
replicas=3 means it run in 3 node, manager is also node.</br>
Use nginx web browser to see rinning container.</br> 
<strong>$ docker service create --replicas 3 -p 80:80 --name web1 nginx</strong></br>
18

<li>Check the status:</li>
<strong>$ docker service ls</strong></br>
19
<li>Running status:</li>
<strong>$ docker service ps web1</strong></br>
20
services running on all nodes</br>

<li>Check on the browser by giving ip for all nodes</li>
Take the url port and run on browser.</br> 
21
</ol>
---------------------------------------------------------------------------------</br>
<li>Step 8: Scale service up and down.</li>
<ol>
<li>We can increase or decrease the countS for example web1=6, till now it running in 3 nodes </li> 
<strong>$ docker service scale web1=6</strong></br>

<li>Check the services in manger terminal.</li>
<strong>$ docker service ls</strong></br>
<strong>$ docker service ps web</strong></br>
23
we can see 3 new services is created in manager1 terminal.</br> 

<li>if we run 'docker ps' command inside the manager1 terminal, we can see service is running on 2 containers on manager1 </li>
24
now 2 container is running in manager1, other 2 in worker1.1 and rest 2 in worker1.2</br>

<li>check in worker1.1 node</li>
<strong>$ docker ps</strong></br>
same for worker1.2 node </br>
25
 
<li>now decrease the services=2</li>
now it running in worker1.1, and in worker1.2</br>
26

<li>all details about the node</li>
Inspecting Nodes (this command can run only on manager node)</br>
<strong>$docker node inspect nodename(worker1.1)</strong></br>

<li>inspect in running terminal </li>
<strong>$ docker node inspect self</strong></br>
</ol>
---------------------------------------------------------------------------------------</br>
<li>Step 9: Update service.</li>
<ol>
<li>When your service is running on multiple machines and you have to make some updates.</li>
docker service update --image imagename:version web1</br>
<strong>$ docker service update --image nginx:1.14.0 serviceName</strong></br>
27
</ol>

---------------------------------------------------------------------------------------</br>
<li>Step 10: Shutdown node</li>
<ol>
<li>Command to shutdown the node.</li>
<strong>$ docker node update --availability drain worker1.2</strong></br>
<li>Check the AVAILABILITY of the worker1.2</li>
<strong>$ docker node ls</strong></br>
28
</ol>
-----------------------------------------------------------------------------------------</br>
<li>Step 11: Remove service</li>
<ol>
<li>docker service rm serviceName it will remove the node from all the machine.</li>
<strong>$ docker service rm web1<strong></br>
29

<li>docker swarm leave : to leave the swarm</li>
<ol>
Stop workers nodes from main terminal and then check the status of nodes in manager1 terminal.</br>
docker-machine stop machineName : to stop the machine</br>
<strong>$ docker-machine stop worker1.1</strong></br>
<strong>$ docker-machine stop worker1.2</strong></br>
30
Now check in manager1 terminal</br>

You can see the status of workers is down.</br>
<strong>$ docker node ls</strong></br>
31
</ol>

<li>docker-machine rm machineName : to remove the machine. Run command in main terminal.<li>
<strong>$ docker-machine rm worker1.1</strong></br>
<strong>$ docker-machine rm worker1.1</strong></br>
32
</ol>
</ol>

















































