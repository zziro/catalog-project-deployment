# catalog-project-deployment

In this project we will be deploying the sport catalog project. 
Project URL: [54.152.38.77]

## Launch Amazon Lightsail instance

In order to make accesible the project we will be using amazon services. The project will be using [Amazon Lightsail].

If you do not have an Amazon Lightsail account. Please create on at [Amazon Console].

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

* Creating new User
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

* Configure firewall
 	* sudo ufw allow 2200/tcp
 	* sudo ufw allow 80/tcp
 	* sudo ufw allow 123/udp
 	* sudo ufw enable


## Application's deployment

	Once the server have configured. Is time to deploy the application.

* Install dependencies:
    * sudo apt-get update
    * sudo apt-get upgrade
	* sudo apt-get install apache2
	* sudo apt-get install libapache2-mod-wsgi python-dev
	* sudo apt-get install git
	* sudo apt-get install python-pip python-flask python-sqlalchemy 		python-psycopg2
	* sudo apt-get install libpq-dev python-dev
	* sudo apt-get install postgresql postgresql-contrib
	* sudo pip install oauth2client requests httplib2
	* sudo apt-get install postgresql
	* pip install --upgrade pip
	* pip install werkzeug==0.8.3
	* pip install flask==0.10
	* pip install Flask-Login==0.1.3
	* pip install oauth2client
	* pip install requests
	* pip install httplib2

* Clone the application
	* Change directory to www file: cd /var/www
	* Create a directory: mdir catalog
	* Change direcory to catalog folder: cd catalog
	* Clone the catalog project: git clone https://github.com/zziro/fullstack-nanodegree-vm.git catalog

* Create a WSGI file
	* Change directory to aplication's path: cd /var/www/catalog/catalog/vagrant/catalog 
	* sudo vim catalog.wsgi
	* Inside the file write the following:

		import sys
		import logging

		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0, '/var/www/catalog/catalog/vagrant/catalog')

		from application import app as application
		application.secret_key='CLIENT_SECRET'

	Note: application name should match with the application python file.

* Configure virtualhost
	* Change directory to : cd /etc/apache2/sites-available/catalog.conf

	![alt text][config-virtualhost]

	* Enable our website
		sudo a2ensite catalog.conf
		sudo service apache2 reload
	* Disable the default website
		sudo a2dissite 000-default.conf
		sudo service apache2 reload

* Configuration in application.py

	* Change directory to : cd /var/www/catalog/catalog/vagrant/catalog/
	* Open the application.py file: sudo vim application.py
	* Update the CLIENT_ID and CLIENT_SECRET lines to: 

		![alt text][client-secret-config]
    
    * Configure the engine connection:
    engine = create_engine('postgres://catalog:[PASSWORD]@localhost/catalog')

    * At the end of the file change the IP '0.0.0.0' to the Amazon Public IP Address and port 80.
    app.run(host='54.152.38.77', port=80)

* Configure database
	* Login with default user postgres : sudo -u postgres psql postgres
	* CREATE USER catalog WITH PASSWORD 'PASSWORD';
	* ALTER USER catalog CREATEDB;
	* CREATE DATABASE catalog WITH OWNER catalog;
	* Connecting to the catalog database: $\c catalog
	* REVOKE ALL ON SCHEMA public FROM public;
	* GRANT ALL ON SCHEMA public TO catalog;
	* Quit of the database: $\q

* Confite the database_setup.py file
	* sudo vim /var/www/catalog/catalog/vagrant/catalog/database_setup.py
	* Change the engine connection:
	  engine = create_engine('postgres://catalog:[PASSWORD]@localhost/catalog')
	* Run database_setup.py: python database_setup.py
	* Restarting the apache server: sudo service apache2 restart
	* Type the Amazon public IP address in the browser: 54.152.38.77 and the web shoould be live.


[Amazon Lightsail]:https://aws.amazon.com/lightsail/
[Amazon Console]:https://aws.amazon.com/console/
[logo]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/aws-services.png
[logo-connect-ssh]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/connect-ssh.png
[ssh-key-pairs]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/ssh-key-pairs.png
[custom-firewall]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/custom-firewall.png
[ssh-connect-ubuntu]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/test-connect-ubuntu.png
[54.152.38.77]:http://54.152.38.77/
[client-secret-config]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/client-secret-config.png
[config-virtualhost]:https://github.com/zziro/catalog-project-deployment/blob/master/screenshots/config-virtualhost.png
