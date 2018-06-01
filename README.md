# catalog-project-deployment

In this project we will be deploying the sport catalog project. 

In order to make accesible the project we will be using amazon services. The project will be using [Amazon Lightsail].

If you do not have an Amazon Lightsail account. Please create on at [Amazon Console].

## Launch Amazon Lightsail instance

* Once you have you Amazon account, login on [Amazon Console] and click on Services and you will see a bunch of amazon's services. Change the origin and select N. Virginie. From there, Select Lightsail![alt text][logo].

* This will prompt you to the next page![alt text][logo-connect-ssh].
The page show important things. 
	Connect using SHH. <br />
	Public IP: 54.152.38.77 <br />
	Username: ubuntu <br />

At the bottom on the left click on Account Page link to download the default private key. Normally, it takes place at Downloads folder.

* On the page, click on Download![alt text][ssh-key-pairs].

* Click on Networking tab -> Firewall, and two custom ports: 123 and 2200. ![alt text][custom-firewall]:

* Connection to Amazon Lightsail:
	* Rename file: sudo mv LightsailDefaultPrivateKey-us-east-1.pem private_key.pem <br />
	* Move .pem file to .ssh folder: sudo mv private_key.pem ~/.ssh <br />
	* Change Directory (cd): cd .ssh <br />
	* Change permissions: sudo chmod 600 private_key.pem <br />
	* Test connection to AWS Lightsail: sudo ssh -i ~/.ssh/private_key ubuntu@54.152.38.77 <br />

* Createting new User
	* Once logged in in Amazon Lightsail, change the user to super user: sudo su (this will be allow to make changes in the server with user root).
	* Create new User: sudo adduser grader
	* Create new file: sudo vim /etc/sudoers.d/grader
	* In the new file, write: grader ALL=(ALL:ALL) ALL (to save changes ESC and :wq!)
	* Open the file hosts: sudo vim /etc/hosts
	* Add the following host: 127.0.0.1 ip-172-26-5-25

* Generate the private key
	* In another terminal: sudo ssh key-gen -f  ~/.ssh/private_key.rsa	
	* Find the key generated: sudo cat ~/.ssh/private_key.rsa.pub
	* Copy  the key generated.
	* Go to another terminal, navigat to grader's folder: cd /home/grader
	* Create the .ssh directory: mkdir .ssh
	* Create the authorized file: touch .ssh/authorized_keys (here will store the private key copied).
	* Edit the authorized_file: sudo vim .ssh/authorized_key
	* Paste here the key copied.
	* Granting permission to .ssh folder: sudo chown 700 /home/grader/.ssh
	* Granting permission to authorized_key file: sudo chown 644 /home/grader/.ssh/authorized_keys
	* Change the owner from root to grader: sudo chown -R grader:grader /home/grader/.ssh/
	* Configuring Port, PasswordAuthentication and PermitRootLogin:
		* sudo vim /etc/ssh/sshd_config
		* Find PasswordAuthentication and as no : PasswordAuthentication no
		* Find Port and chagne to 2200: Port 2200
		* Find PermitRootLogin and set up as no: PermitRootLogin no
	* Restart the ssh service: sudo service ssh restart
	* Write 'exit' to go out 'logout'.
	* Testing the connectio as grader: ssh -i ~/.ssh]/private_key.rsa grader@54.152.38.77

















	





## Deployment





[Amazon Lightsail]:https://aws.amazon.com/lightsail/
[Amazon Console]:https://aws.amazon.com/console/
[logo]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/aws-services.png
[logo-connect-ssh]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/connect-ssh.png
[ssh-key-pairs]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/ssh-key-pairs.png
[custom-firewall]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/custom-firewall.png
[ssh-connect-ubuntu]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/test-connect-ubuntu.png
