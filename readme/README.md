


- We need to configure Docker Service from the localhost to work as a Client and not as a Server. To configure edit the file /usr/lib/systemd/system/docker.service as follows:

`vim /usr/lib/systemd/system/docker.service `

add line:

![configure docker services](/images/dconf.JPG)


This will allow any port to access the Docker service remotely from other systems. Enter the following command in the remote system:

`export DOCKER_HOST=192.168.99.103:2375`


- open the Jenkins WebUI to configure the Clouds and Nodes before configuring our Jobs

**Go to Manage Jenkins > Manage Nodes and Clouds > Configure Clouds > Add a new Cloud > Docker**

Set the Credentials as follows:

Set any name and enter the Docker Host URL of the remote Cluster along with the Port number you have set while configuring the Docker.

![configure cloud nodes](/images/configcloud.JPG)
