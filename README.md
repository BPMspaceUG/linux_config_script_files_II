# BPMspaceCloud

## DockerHosts Initial Setup

### Prerequisites
Linux server with DEBIAN 9 (Stretch) and ssh access and named:
- docker_master_001.bpmspace.net
- docker_node_001.bpmspace.net
- docker_node_002.bpmspace.net

### for setup the 1st Master 
>wget https://raw.githubusercontent.com/BPMspaceUG/BPMspaceCloud/master/bpmspace_setup_dockerhost_stretch.sh
>chmod +x bpmspace_setup_dockerhost_stretch.sh
>./bpmspace_setup_dockerhost_stretch.sh MASTER docker_master_001.bpmspace.net

### for setup the 1st NODE (WORKER) 
>wget https://raw.githubusercontent.com/BPMspaceUG/BPMspaceCloud/master/bpmspace_setup_dockerhost_stretch.sh
>chmod +x bpmspace_setup_dockerhost_stretch.sh
>./bpmspace_setup_dockerhost_stretch.sh WORKER docker_node_001.bpmspace.net

### for setup the 2nd NODE (WORKER) 
>wget https://raw.githubusercontent.com/BPMspaceUG/BPMspaceCloud/master/bpmspace_setup_dockerhost_stretch.sh
>chmod +x bpmspace_setup_dockerhost_stretch.sh
>./bpmspace_setup_dockerhost_stretch.sh WORKER docker_node_002.bpmspace.net

## DockerSwarm Initial Setup

BPMspaceCloud DockerSwarm is based on the Imixs-Cloud - https://github.com/imixs/imixs-cloud

_Imixs-Cloud_ is an open infrastructure project, providing a lightweight [docker](https://www.docker.com/) based container environment for production business applications. The main objectives of this project are **simplicity**, **transparency** and **operational readiness**. 
The _Imixs-Cloud_ is based on a [docker swarm](https://docs.docker.com/engine/swarm/) cluster environment.
Docker swarm is much easier to setup and in its management compared to a Kubernetes cluster. However, when deciding which platform  to use, you should consider your own criterias. _Imixs-Cloud_ is optimized to **build**, **run** and **maintain** business services in small and medium-sized enterprises.

### I - ssh to docker_master_001 - login as rootmessages
	1) sudo rm /root/bpmspace_setup_dockerhost_stretch.sh 
	2) cd /home/rootmessages
	3) chmod +x ./BPMspaceCloud/dockerswarm/scripts/setup.sh
	4) sudo ./BPMspaceCloud/dockerswarm/scripts/setup.sh <IP@-MASTER>
	
### II - ssh to docker_node_001 AND docker_node_002 - login as rootmessages
	1) sudo rm /root/bpmspace_setup_dockerhost_stretch.s
	2) docker swarm join --token <TOKEN FROM STEP I-4>  <IP@-MASTER>:2377
	
### III - ssh to docker_master_001 - login as rootmessages
	1)TEST network
		docker network ls | grep overlay
		Output should look like
			NETWORK ID	NAME		DRIVER		SCOPE
			1q17asdn5z6r	cloud-net	overlay		swarm
			sadfq830v4fb	ingress		overlay		swarm
			dzptc459kk30	proxy-net	overlay		swarm
	2) TEST Swarm 
		docker node ls
		Output should look like
			ID				HOSTNAME			STATUS	AVAILABILITY	MANAGER STATUS	ENGINE VERSION
			5ufqco634dq43usqulgnfvkb *	docker_master_001.bpmspace.net	Ready	Active		Leader		19.03.5
			vmk88y5o17q654b6az8l9p8x	docker_node_001.bpmspace.net	Ready	Active				19.03.5
			in0o2qh9iv8342ggzxcxiw15	docker_node_002.bpmspace.net	Ready	Active				19.03.5
	3) echo -n password | docker secret create portainer-admin-pass -
		(see https://github.com/portainer/portainer/issues/2816)
	4) sudo docker stack deploy -c ./imixs-cloud/management/portainer/docker-compose.yml Portainer
	5) DOCKER SECRET NOT WORKING AT THE MOMENT WITH PORTAINER - so set passwd for user admin at first start
	6) The Stack has do be added again. only portainer can be fully controlde by Portainer
![Portainer](https://github.com/BPMspaceUG/BPMspaceCloud/blob/master/_img/PORTAINER_SETUP_STACK_Portainer.png "Portainer")

	
### IV - setup TraefikReverseProxy
![TraefikReverseProxy](https://github.com/BPMspaceUG/BPMspaceCloud/blob/master/_img/PORTAINER_SETUP_STACK_TraefikReverseProxy.png "TraefikReverseProxy")