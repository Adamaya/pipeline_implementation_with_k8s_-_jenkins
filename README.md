# pipeline_implementation_with_k8s_-_jenkins
automation is required everywhere create and test a deployment using kubernetes orchestration tool and jenkins pipeline.

## Steps:
### 1.	Create container image that’s has Jenkins installed  using dockerfile and 2.	When we launch this image, it should automatically starts Jenkins service in the container.

- Dockerfile of jenkins-k8s
**Note:**- add your minikube credential
```
FROM centos
RUN yum install wget -y
RUN yum install java-11-openjdk-devel -y
RUN yum install sudo -y
RUN yum install git -y
RUN sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
RUN sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
RUN yum install jenkins -y

# for configuring kubectl
COPY kubectl /usr/bin
RUN sudo chmod +x /usr/bin/kubectl

# add your client.key, client.crt and ca.crt
COPY client.key /root
COPY client.crt /root
COPY ca.crt /root
RUN mkdir .kube
COPY .kube /root/.kube

EXPOSE 8080

```


- run the following command to build the jenkins image. **you can change the image name in my case I am using 'adamayasharma/jenkins-k8s' image name


`docker build -t adamayasharma/jenkins-k8s .`

- run the docker container of build image. **I am mounting /root/devops/Task_3_perform_the_same_task2_with_k8s to container at /home because my deployment yml scripts are situateed in /root/devops/Task_3_perform_the_same_task2_with_k8s folder**

`docker container run -dit -p 8085:8080 --name jenkins-k8s  -v /root/devops/Task_3_perform_the_same_task2_with_k8s:/home adamayasharma/jenkins-k8s`

- open link in web address bar `<ip of VM>:8085`

- to see the password of jenkins run command.

`docker exec <name> cat /var/lib/jenkins/secrets/initialAdminPassword`

- login and configure the jenkins.


###  Job1 : Pull  the Github repo automatically when some developers push repo to Github.

- go to plugin managerand install github plugins.

- go to job1 -> click on configure

- now do as given in images.
![job1_1](/images/job1_1.JPG)
![job1_2](/images/job1_2.JPG)

- save it

```
count=$(ls | grep .php | wc -l)
if [[ $count -gt 0 ]]
then
cp -vr * /home/php
exit 0
else
exit 1
fi

count=$(ls | grep .php | wc -l)
if [[ $count == 0 ]]
then
cp -vr * /home/html
exit 0
else
exit 1
fi
```
### Job2 : By looking at the code or program file, Jenkins should automatically start the respective language interpreter install image container to deploy code ( eg. If code is of  PHP, then Jenkins should start the container that has PHP already installed ).

- go to job 2 -> click on configure

- no do as given in image.

![checking the format](/pictures/4_Task2.PNG)

- add the code

![checking the format](/pictures/5_Task2.PNG)

- apply and save
```
count=$(ls /var/lib/jenkins/workspace/job1_pull_repository_build_image_push_dockerhub | grep .php | wc -l)
if [[ $count -gt 0 ]]
then
if kubectl get deployment | grep php
then
exit 0
else
kubectl create -f /home/php-deployment.yml
fi
else
if kubectl get deployment | grep http
then
exit 0
else
kubectl create -f /home/http-deployment.yml
fi
fi
```

### 6.	Job3 : Test your app if it  is working or not.
- add the code in execute shell

![](/pictures/11_Task2.PNG)

- apply and save

```
sleep 5
status=$(curl -o /dev/null -s -w "%{http_code}" 192.168.99.102:30600)
if [[ $status == 200 ]]
then
echo "web application working"
else
echo "web application is not working"
fi
```



```
status=$(curl -o /dev/null -s -w "%{http_code}" 192.168.99.102:30600)
if [[ $status == 200 ]]
then
exit 0
else
exit 1
fi
```
