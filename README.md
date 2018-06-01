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
	Note: LightsailDefaultPrivateKey-us-east-1.pem's content is at the bottom of this file.

* Creating new User
	* Once logged in in Amazon Lightsail, change the user to super user: sudo su (this will allow to make changes in the server with user root).
	* Create new User: sudo adduser grader
	* Create new file: sudo vim /etc/sudoers.d/grader
	* In the new file, write: grader ALL=(ALL:ALL) ALL (to save changes ESC and :wq!)
	* Open the file hosts: sudo vim /etc/hosts
	* Add the following host: 127.0.0.1 ip-172-26-5-25

* Generate the private key
	* In another terminal: sudo ssh key-gen -f  ~/.ssh/private_key.rsa	
	* Find the key generated: sudo cat ~/.ssh/private_key.rsa.pub
	* Copy  the key generated.
	* Go to another terminal, navigate to grader's folder: cd /home/grader
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
		* Find Port and change to 2200: Port 2200
		* Find PermitRootLogin and set up as no: PermitRootLogin no
	* Restart the ssh service: sudo service ssh restart
	* Write 'exit' to go out and then 'logout' to disconnect.
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
	* Create a directory: mkdir catalog
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

	* Enable the website: <br />
		sudo a2ensite catalog.conf <br />
		sudo service apache2 reload <br />
	* Disable the default website <br />
		sudo a2dissite 000-default.conf <br />
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


LightsailDefaultPrivateKey-us-east-1's content:

-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAqBVHYO6t31auSx5M00EjwGIzG9Tep+Ds20XyDJNFa9Ln+hRe
ofCDm8YAMl4nfnuGO7YSlYhaxCK3L1HBOE3hXnPZyqPmX51GfUWHEbN6w64FHWyf
SmSbQ9Pa82QG8NWSLjtFWCZMICTFeRQCsi+4o8vCzISStUNGAmZJ1FqTEXBY63rl
G+lBnRoYZ4tQh97pXowz5B0RKOygLIH9uRnrYk4PmRhSYFnFF6fVW9+fpnf9FTK3
Mk5X4lvtiinpztaswQxlod28sZ0tBPriMPoZBM6sMTMjJ5jhR257KCsejcNHKILL
r0/BweyZzaDi0rhmEcwmzn9Dz/PD8sb+JRYpTwIDAQABAoIBAGLb8RmQCG/9uZGx
BpqSC4cRuuG5uCOhZW4qm4zN6dzbNVmfNXGl1qJ+OItnWW9JE9B27ENK6afGi1un
iC+njtBQ714CPHMsO9NeW20L1T7WUWfXBXFp7vBb4MGaqYJo+wt6DPUlwUx+lC78
mAFZlA2ywKYtq2Q1Zy3uH6pog2uTZeYEz01EJVXVdJx1xhdzWvrj+Tl5BxstIBjU
pT4XvOGx8uOcj6ygjGAU+urHb4AfolqJ5Y8t8NtRGMZGMA0v9WmfXZZPqh/Y9ckY
I6hUMR/ZUU0g+YFLBdawFDx7RmP3FFQktSSeI295dRfP1EP0vIiRis/8lJuNNZjQ
qU+X4VECgYEA35wYS6ET+HmhuPewXY4ChrkknEvtREj0H7ItDbZwWTGnc6kEfnFN
4OloH3oHJayD8Ia1gooYeCGt5DAPmLm10LDdfPfrkhZlHH6tS4PapgtOlgXS/TsM
YrNKF01QinguckwSV8EJjqrxsqsnh/RUOjyO4+VCJOq2TE5UVpUPyJUCgYEAwG4k
u4y9+GfYT8249GcF2fo49JP2Cu5Qi5iLmI0khhRyI1iM8gm+U4NemYZkFyexHRsZ
dtn/cNUpiijllOwOyn4o79NQTIiup/Y5hslxXjMysXS/g8CNdOJWtmrQ/ujtkncj
NGPimsPMDPFEsCH0U5fVD8nhzBpK/rO4rkgyXVMCgYEArKiu6dbawMXKZjR67gI1
WSmkZzsJXeym88J0n137qiN5FQmw+qEqbTxMteFXm3N1/UdG6CERe9EloEisAlWD
Wjz3CQO11fuM30/BJ8oMDqI0cArXO8lYau3di9ibFBBwbLjogPIBGzRLpJYb1BKI
5t7TxozDYhYJ5SmmXaSDp3ECgYEAocF0mzs5zX/tc1fpK3kWkB8olV9zKckeZ9n5
Lmkm1JaHQ3MCN8s89BKwCLnRlS1GUsMe6qthcEWfP4YVRtQPsuPOIZsuGseimYn+
3XCkHZ1H9ae//U51GlXQslKkEENYwQ/oxfdY2uPPuOlu5egE6+frr/PyHJHVjLki
e04xGAUCgYEAjTzSs0VMttbWd8ybcW5KPUVkBgToI1Luilsigv5QIQ9sQFr5vIS1
v28k8NKAVF2kBBTwviEyJh+vBdATDEE6VpZDM8iOOlMB+gIka3hBqsBY0l9wb+Rl
pwykgc8ih1nx2VxOERGAhniKlpZ+sSqcpIkAJBPAH+W8LIF9m9Gj90k=
-----END RSA PRIVATE KEY-----


## Refferences

1.- [Amazon Lightsail] <br />
2.- [Permission for .pem file] <br />
3.- [Configure host] <br />
4.- [WSGI mode to deploy flas app] <br />
5.- [Disable, Enable default website] <br />
6.- [Configure postgres] <br />
7.- [Vim commands] <br />


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
[Disable, Enable default website]:https://www.codementor.io/curtisgervais/setup-and-configure-apache-virtual-hosts-79kt2nuy6
[Permission for .pem file]:https://unix.stackexchange.com/questions/115838/what-is-the-right-file-permission-for-a-pem-file-to-ssh-and-scp/115981#115981
[Vim commands]:https://www.computerhope.com/unix/vim.htm
[Configure host]:https://askubuntu.com/questions/59458/error-message-sudo-unable-to-resolve-host-user
[WSGI mode to deploy flas app]:http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/
[Configure postgres]:http://suite.opengeo.org/docs/latest/dataadmin/pgGettingStarted/firstconnect.html
