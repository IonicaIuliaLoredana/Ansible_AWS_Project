   
The project's purpose is to deploy the ToDo Application using Ansible.

The steps prior to the deployment are the following:

- Creating 2 instances in AWS: the Bootstrap instance and the Managed Node - the instance for the single node Kubernetes cluster

- Installing Ansible and Docker on the Bootstrap instance

- Connecting the two instances through ssh-keygen and ssh-copy-id

- Building the web application image using Docker and storing it on Dockerhub: https://hub.docker.com/repository/docker/iulia1234/getting-started/general

- Setting the private IP of the Managed Node in the inventory directory in the hosts file, that we will refer to with "hosts:cluster" in the playbooks
 
- Running the create-playbook.yml file on the Bootstrap instance in order to install the necessary applications on the Managed Node  with the command:

*Command to execute from the Infrastructure folder:*
ansible-playbook create-playboook.yml -i inventory/hosts

Through this playbook, swapoff is disabled, older versions of Docker are uninstalled, Docker engine is installed, the cluster and the master node are set up. The last step is to untaint the master node so that pods can be created on it by the Scheduler.

This playbook contains the roles:
 - delete-docker     
 - install-docker    
 - install-k8s
 - k8s-cluster
 - install-calico   

After the one node cluster in set up entirely, the next step is to deploy the application, the database and the ingress controller to the newly created Kubernetes cluster. 

To accomplish this, we run the following Ansible command from the Infrastructure directory:

ansible-playbook deploy-application-playboook.yml -i inventory/hosts -i inventory/variables

This playbook contains the roles: 
 - deploy-app-and-db
 - deploy-ingress
 - deploy-nginx

 To configure easily the deployment, I defined some variables in the file  /inventory/variables
 From there we can modify:
 - the number of replicas for the database ( db_nr_of_replicas ) and  for the application
( app_nr_of_replicas );
 - the namespace for both the application and the database ( namespace ) (They share this variable beacause they are in the same namespace)
 - the name of the containers for mysql ( mysql_container_name ) and for the application ( app_container_name )


Link to running Application: http://k8s-app-appingre-b00c350a75-1114329055.eu-north-1.elb.amazonaws.com/


## Playbooks
### Create Playbook 

Playbook to create the Kubernetes single node cluster with Docker.

*Command to execute from the Infrastructure folder:*
```
ansible-playbook create-playboook.yml -i inventory/hosts
```

### Deploy Application Playbook
Playbook to deploy the application and the database .

*Command to execute from the Infrastructure folder:*
```
ansible-playbook deploy-application-playboook.yml -i inventory/hosts

```



 
