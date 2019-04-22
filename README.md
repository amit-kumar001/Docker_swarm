# Docker_swarm    

![600x400](https://user-images.githubusercontent.com/47202519/56484411-1858a480-64ed-11e9-9107-09e009a957f4.jpg)</br>
<ol>
A <strong>swarm</strong> is a group of machines that are running Docker and joined into a cluster </br>
swarm turns multiple docker hosts into a single, virtual docker host.</br>
Docker Swarm is a tool for Container Orchestration</br>

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
<li>Docker 1.13 or higher</li>  </br>  
 
![1](https://user-images.githubusercontent.com/47202519/56483999-ed6d5100-64ea-11e9-90e8-cd0807a00c9e.png)</br>

<li>Docker Machine (pre installed for Docker for Windows and Docker for Mac)</li>
</ol>  

----------------------------------------------------------------------------------------

On <strong>mac OS</strong> and <strong>Windows</strong>, Machine is installed along with other Docker products when you install the Docker for Mac, Docker for Windows, or Docker Toolbox.</br>
But in ubuntu you need to install the machine</br>
Command to install docker-machine in ubuntu</br>
<ol>
<li><strong>base=https://github.com/docker/machine/releases/download/v0.16.0 &&</br>
curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&</br>
sudo install /tmp/docker-machine /usr/local/bin/docker-machine</strong></li>  </br>

![2](https://user-images.githubusercontent.com/47202519/56484002-f100d800-64ea-11e9-92c4-de32558f5761.png)</br>

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

![3](https://user-images.githubusercontent.com/47202519/56484004-f4945f00-64ea-11e9-9c72-ef19aaab237f.png)</br>

<li>Now create docker machine manager </li>
<strong>$ docker-machine create --driver virtualbox manager1</strong></br>  

![4](https://user-images.githubusercontent.com/47202519/56484006-f827e600-64ea-11e9-99b2-12c8370507af.png)</br>

<li>To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run:</li>
<strong>$ docker-machine env manager1</strong></br>  

![5](https://user-images.githubusercontent.com/47202519/56484012-fcec9a00-64ea-11e9-9a24-bb03fbf78868.png)</br>

</ol>  

------------------------------------------------------------------------------

<li>Step 2: Check machine created successfully</li>
<ol>
<li>docker-machine ls to list out the machines that we have</li>
<strong>$ docker-machine ls</strong></br>  

![6](https://user-images.githubusercontent.com/47202519/56484045-3de4ae80-64eb-11e9-9f64-14c8f63bd3e2.png)</br>

<li>The machine has been created, it's active the driver is VirtualBox.</li>

<li>IP of this particular machine</li>
<strong>$ docker-machine ip manager1</strong></br>

<li>Now we need to create more machine with same command but change the name of the machine.</li>
<strong> $ docker-machine create --driver virtualbox worker1.1</strong></br>
 <strong>$ docker-machine create --driver virtualbox worker1.2</strong></br>
In short we have 3 nodes out of which one will be manager 2 will be workers.</br>  

![7](https://user-images.githubusercontent.com/47202519/56484047-40df9f00-64eb-11e9-9e27-ef6d474a2baf.png)</br>

</ol>  

----------------------------------------------------------------------------------

<li>Step 3: <strong>SSH</strong>(connect) to docker machine.</li>
<ol>
Open new terminal (ctrl+shift+t)</br>
<li>Log into or run a command on a machine using SSH.</li>
To login, just run docker-machine ssh machinename:</br>
We will look how to do ssh into a docker machine directly and to use it as a <strong>VM machine.</strong></br>  

![9](https://user-images.githubusercontent.com/47202519/56484052-4806ad00-64eb-11e9-94e1-19fd0cb230c4.png)</br>

<li>Every time we need to open new terminal to link with ssh</li>
<strong>$ docker-machine ssh manager1</strong></br>
<strong>$ docker-machine ssh worker1.1</strong></br>
<strong>$ docker-machine ssh worker1.2</strong></br></br>

<strong>SSH key</strong>
You can link your Cloud and Service providers so that Docker Cloud can provision and manage swarms on your behalf. For this, you need an SSH key to authenticate Docker to your provider.</br>
SSH is a secure protocol for accessing remote machines and applications. It provides authentication and encrypts data communication over insecure networks.</br>
</ol>  

---------------------------------------------------------------------------

<li>Step 4: Initialize Docker Swarm.</li>
<ol>
<li>Check node_id</li>
<strong>$ docker-machine ip manager1</strong></br>

<li>Now use same terminal in which we add a link between manager1 and ssh key.</li>
<strong>$ docker swarm init --advertise-addr MANAGER_IP</strong></br>
<strong>$ docker swarm init --advertise-addr 192.168.99.100</strong></br>  

![10](https://user-images.githubusercontent.com/47202519/56484055-4a690700-64eb-11e9-9b44-ff782649e969.png)</br>

We can add more manager with same command.</br>
<li>Check the node status</li>
This command is run in only manager terminal(this command will work only in swarm manager and not in worker).</br>
<strong>$ docker node ls</strong></br>  

![11](https://user-images.githubusercontent.com/47202519/56484064-4fc65180-64eb-11e9-934a-e653838657cf.png)</br>

</ol>  

-------------------------------------------------------------------------------

<li>Step 5: Join workers in the swarm.</li>
<ol>
<li>Command for joining as worker.</li>
Run command in manager1 node(terminal). </br>
This will give command to join swarm as worker</br>
<strong>$ docker swarm join-token worker</strong> </br> 

![12](https://user-images.githubusercontent.com/47202519/56484068-53f26f00-64eb-11e9-8c2f-2f789283466e.png) </br> 

<li>Copy the swarn join command and run in worker-machine terminal to add worker for the manager node</li>
<strong>$ docker swarm join --token SWMTKN-1-1627nzpuotunfseghhlp0l8f5osvikkyi5cqmqezspoq2n1w8b-39fj8930ndg14mq20oy1ey86m 192.168.99.100:2377</strong></br>  

![13](https://user-images.githubusercontent.com/47202519/56484069-56ed5f80-64eb-11e9-8930-578cb2f85e5d.png)</br>

<li>Now go back in to manager terminal and check the status of all nodes</li>
<strong>$ docker node ls</strong></br>  

![14](https://user-images.githubusercontent.com/47202519/56484159-c6fbe580-64eb-11e9-8fba-54a79b09ff87.png)</br>

<li>Another way to join manager</li>
docker swarm join-token manager</br>
This will give command to join swarm as manager.</br>
<strong>docker swarm join --token SWMTKN-1-1627nzpuotunfseghhlp0l8f5osvikkyi5cqmqezspoq2n1w8b-1fv7a9blk474i605hbwz7vt6l 192.168.99.100:2377</strong></br>  

![15](https://user-images.githubusercontent.com/47202519/56484163-d0854d80-64eb-11e9-9bdb-26b153ca9ff0.png)</br>

SSH into worker node(machine) and run command to join swarm as worker.</br>
In Manager Run command - docker node ls to verify worker is registered and is ready.</br>
Do this for all worker machines.</br>
</ol>  

-----------------------------------------------------------------------------------

<li>Step 6: On manager run standard docker commands.</li>
<ol>
<li>Check swarm section in which all details about swarm no of manager, nodes(manager is also node) etc.</li>
<strong>$ docker info</strong>  </br>
 
![16](https://user-images.githubusercontent.com/47202519/56484165-d418d480-64eb-11e9-8607-ebe8bd13f876.png)</br>

<li>Now check docker swarm command options.</li> 
<strong>$ docker swarm </strong>  </br>

![17](https://user-images.githubusercontent.com/47202519/56484170-d713c500-64eb-11e9-8c43-07656e876858.png)</br>

</ol>   

----------------------------------------------------------------------------------

<li>Step 7: Run containers on Docker Swarm.</li>
<ol>
<li>Now we will create a services, and run these services to create replicas with count(number) of replica.</li> 
Run this command in manager1 terminal.</br>
docker service create --replicas 3 -p 80:80 --name service_Name nginx</br>
replicas=3 means it run in 3 node, manager is also node.</br>
Use nginx web browser to see rinning container.</br> 
<strong>$ docker service create --replicas 3 -p 80:80 --name web1 nginx</strong></br>  

![18](https://user-images.githubusercontent.com/47202519/56484174-da0eb580-64eb-11e9-8897-1b4aba070cc2.png)</br>

<li>Check the status:</li>
<strong>$ docker service ls</strong></br>  

![19](https://user-images.githubusercontent.com/47202519/56484177-dc710f80-64eb-11e9-854a-eee4d17c0275.png)</br>

<li>Running status:</li>
<strong>$ docker service ps web1</strong></br>  

![20](https://user-images.githubusercontent.com/47202519/56484182-ded36980-64eb-11e9-9a58-c2f6891c558f.png)</br>

services running on all nodes</br>

<li>Check on the browser by giving ip for all nodes</li>
Take the url port and run on browser.</br>   

![21](https://user-images.githubusercontent.com/47202519/56484185-e135c380-64eb-11e9-866a-8b0f9b1b3a79.png)</br>

</ol>  

---------------------------------------------------------------------------------

<li>Step 8: Scale service up and down.</li>
<ol>
<li>We can increase or decrease the countS for example web1=6, till now it running in 3 nodes </li> 
<strong>$ docker service scale web1=6</strong></br>

<li>Check the services in manger terminal.</li>
<strong>$ docker service ls</strong></br>
<strong>$ docker service ps web</strong></br>  

![23](https://user-images.githubusercontent.com/47202519/56484250-3d004c80-64ec-11e9-8281-c9513f4635a9.png)</br>

we can see 3 new services is created in manager1 terminal.</br> 

<li>if we run 'docker ps' command inside the manager1 terminal, we can see service is running on 2 containers on manager1 </li>   </br>

![24](https://user-images.githubusercontent.com/47202519/56484258-44275a80-64ec-11e9-82fc-7885d2b505de.png)</br>

now 2 container is running in manager1, other 2 in worker1.1 and rest 2 in worker1.2</br>

<li>check in worker1.1 node</li>
<strong>$ docker ps</strong></br>
same for worker1.2 node </br>  

![25](https://user-images.githubusercontent.com/47202519/56484261-47bae180-64ec-11e9-805f-2b62f9eea0f1.png)</br>
 
<li>now decrease the services=2</li>
now it running in worker1.1, and in worker1.2</br>  

![26](https://user-images.githubusercontent.com/47202519/56484265-4ab5d200-64ec-11e9-845e-0b5b28c302a8.png)</br>

<li>all details about the node</li>
Inspecting Nodes (this command can run only on manager node)</br>
<strong>$docker node inspect nodename(worker1.1)</strong></br>

<li>inspect in running terminal </li>
<strong>$ docker node inspect self</strong></br>
</ol>  

---------------------------------------------------------------------------------------

<li>Step 9: Update service.</li>
<ol>
<li>When your service is running on multiple machines and you have to make some updates.</li>
docker service update --image imagename:version web1</br>
<strong>$ docker service update --image nginx:1.14.0 serviceName</strong></br>  

![27](https://user-images.githubusercontent.com/47202519/56484268-4db0c280-64ec-11e9-899f-f70f9283117e.png)</br>

</ol>  

---------------------------------------------------------------------------------------

<li>Step 10: Shutdown node</li>
<ol>
<li>Command to shutdown the node.</li>
<strong>$ docker node update --availability drain worker1.2</strong></br>
<li>Check the AVAILABILITY of the worker1.2</li>
<strong>$ docker node ls</strong></br>   

![28](https://user-images.githubusercontent.com/47202519/56484274-50abb300-64ec-11e9-946c-e8f9d041b42b.png)</br>

</ol>  

-----------------------------------------------------------------------------------------

<li>Step 11: Remove service</li>
<ol>
<li>docker service rm serviceName it will remove the node from all the machine.</li>
<strong>$ docker service rm web1</strong></br>  

![29](https://user-images.githubusercontent.com/47202519/56484276-530e0d00-64ec-11e9-830b-5c66f2fa091f.png)</br>

<li>docker swarm leave : to leave the swarm</li>
<ol>
Stop workers nodes from main terminal and then check the status of nodes in manager1 terminal.</br>
docker-machine stop machineName : to stop the machine</br>
<strong>$ docker-machine stop worker1.1</strong></br>
<strong>$ docker-machine stop worker1.2</strong></br>  

![30](https://user-images.githubusercontent.com/47202519/56484279-56a19400-64ec-11e9-8142-e86079cec0af.png)</br>

Now check in manager1 terminal</br>

You can see the status of workers is down.</br>
<strong>$ docker node ls</strong></br>  

![31](https://user-images.githubusercontent.com/47202519/56484295-5f926580-64ec-11e9-87bf-882189e35ea8.png)</br>

</ol>

<li>docker-machine rm machineName : to remove the machine. Run command in main terminal.<li>
<strong>$ docker-machine rm worker1.1</strong></br>
<strong>$ docker-machine rm worker1.1</strong></br>  

![32](https://user-images.githubusercontent.com/47202519/56484299-628d5600-64ec-11e9-8cea-567b6c6ee101.png)</br>

</ol>
</ol>
