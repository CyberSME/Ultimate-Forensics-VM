**Preperation**  
Install LUbuntu latest version  
(I want a low resource host, therefore I chose LUbuntu)  

OR

Use Qubes OS and the debian-9 template.  <-- DO THIS :) --> https://www.qubes-os.org/


***Now install Docker stuff for LUbuntu***  
```bash
apt-get install docker  
apt-get install docker.io  
# update docker to auto start
sudo update-rc.d docker defaults  
```


***Install Docker on Qubes debian-9 template***
```
apt-get install apt-transport-https dirmngr
echo 'deb https://apt.dockerproject.org/repo debian-stretch main' >> /etc/apt/sources.list
apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
apt-get update
apt-get install docker-engine
# lets have docker start on boot
update-rc.d docker defaults
```


***add user to docker group to grant docker admin privs***
```
# best to reboot after running this command, at least log off and back on
usermod -a -G docker $USER
```


**Docker images to pull**  
```bash
# Javascript deobfuscation and analysis
docker pull remnux/jsdetox  
# needed to create forensics docker images  
docker pull ubuntu:14.04  
# needed to create SecurityOnion docker image
docker pull ubuntu:16.04  
```


**Create a directory to share with all Dockers**  
```mkdir /data  ```
/tmp will also be shared by default


**Copy each Docker Dockerfile to their own directory**  
/home/docker/sonion/Dockerfile  
/home/docker/remnux/Dockerfile  
/home/docker/sift/Dockerfile  
/home/docker/powershell/Dockerfile  


**Build REMNUX**  
```bash
cd /home/docker/remnux   
docker build -t remnux .  
```
_Remnux package install failures_:  
inetsim  
unrar  


**Build SIFT**  
```bash
cd /home/docker/sift  
docker build -t sift .  
```
_Sift package install failures_:  
mantaray  
nikto  
pytsk3  
rar  
regripper  
unrar  
wine  


**Build SecurityOnion**
```bash
cd /home/docker/sonion  
docker build -t sonion .  

# Now start SO docker to customize it
./start-sonion.sh
# Do your customizations as dockers reset to their initial state unless commited
# open another terminal to commit the customized image
# get the container ID of the running SO docker image
docker ps -a
docker commit <container ID> <new image name>
# now you should see a new image with the name you gave it in the step above
docker images
# exit the original image that is running to stop it
# change the sonion name in the start-sonion.sh script to the name of your new SO image
# now use the new image for all your SO work

```
_SecurityOnion package install failures_:  

**Run Dockers as non-root**
```sudo groupadd docker
sudo gpasswd -a ${USER} docker
sudo service docker restart
sudo reboot
```
WARNING:  https://fosterelli.co/privilege-escalation-via-docker.html  

**Build Powershell**  
https://channel9.msdn.com/Shows/msftazure/Run-PowerShell-Natively-on-Linux-with-Docker
```bash
cd /home/docker/powershell
docker build -t pshell .  
# start pshell docker
# -i -> interactive docker
# -t -> connect docker to terminal
# --rm -> role back to the pre-run state of the docker after it is shutdown
docker run -i -t --rm pshell
```


Use "```service ssh start```" to start sshd and then ssh to the IP of the docker container.  
Refer to the Dockerfile for each image to find the SSH password.  
