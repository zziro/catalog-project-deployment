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

* From terminal to the Downloads folfer:
	Rename file: sudo mv LightsailDefaultPrivateKey-us-east-1.pem private_key.pem <br />
	Move .pem file to .ssh folder: sudo mv private_key.pem ~/.ssh <br />
	Change Directory (cd) : cd .ssh
	Change permissions: sudo chmod 600 private_key.pem
	Test if it is able to connect to AWS Lightsail: sudo ssh -i ~/.ssh/private_key ubuntu@54.152.38.77

	This will prompt a file like this ![alt text][ssh-connect-ubuntu].

* 



	





## Deployment





[Amazon Lightsail]:https://aws.amazon.com/lightsail/
[Amazon Console]:https://aws.amazon.com/console/
[logo]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/aws-services.png
[logo-connect-ssh]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/connect-ssh.png
[ssh-key-pairs]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/ssh-key-pairs.png
[custom-firewall]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/custom-firewall.png
[ssh-connect-ubuntu]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/test-connect-ubuntu.png
