** = code block
%% = bold

Where you see (1/2) in this document , you should repeat the step for both website1.com and website2.com , or follow the steps using your own domain names of choice.


Prequisets :  Raspberry pi , Static IP , SD card ,Rasbain-Jessie 

1. Rasbain Instilation
  Installed Raspbain onto sd card as boot from disk. see folloing tutorial for your OS at : https://www.raspberrypi.org/documentation/installation/installing-images/.

2. Setup Sytem Config:
 boot the raspberry pi , and open terminal once everything has laoded and run sudo rapsi-config.
  2.1 select option 1. Expand Filesystem and select ok (find out why i need to do this ).
  2.2 select option 2. Change User Password and chaneg it to your required password
  2.3 select option 9. Advanced Options -> and Select option A4 SSH -> select yes where it asks if you would like SHH serve to be enabled
  2.4 Finish in the Raspi-config menu and select Yes to reebooting the Pi.

3. Update, Upgrade and Change Message Of The Day:
 After a reboot , either open terminal , or login to your pi over ssh remotely.
  3.1 once in the terminal , type 
	** sudo apt-get upgrade **
 
  3.2	in the terminal , type 
	** sudo apt-get update ** 


 Both 3.1 & 3.2 may take a while. 


  3.3 Change message of the day : (optional)  
	type 
	 ** cd /etc/ **
	 ** sudo nano motd ** 
	you should see a 'message of the day', edit this as you wish and use CTRL+X to save. 

4. Apache2 Instalation & Vhost Setup
	
	( i followed this tutorial &&
	https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-14-04-lts && but repeated the simple steps i 		took)

  4.1 Install Apache2: 
	in the terminal Type 
	** sudo apt-get install apache2 ** 
  
  4.2 Make Directories and demo page:
	in terminal type 
	** cd /var/www/ **
	** sudo mkdir Website1.com **
	** sudo mkdir Website2.com **

	place a index.html file and place 

	** <html>
	   <body>
	   <h1>Hello World website(1/2)</h1>
	   </body>
	   </html>
	**	
  4.3 Permissions : 
	in terminal type  
	** sudo chown -R <USER>:<USER> /var/www/website1.com/ **
	** sudo chown -R <USER>:<USER> /var/www/website1.com/ **

	** sudo chmod -R 755 /var/www **
	
  4.4 Create Virtual host Configurations : 
	navigate to 
	**  cd /etc/apache2/sites-available **
	in terminal type
	** sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/website1.com.conf **
        ** sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/website2.com.conf **
	
	Editing the virtual host : 
	Edit the virtual host .conf files you created erlier by navigating to the vhost config folder by opening the terminal and typing: 
	** /etc/apache2/sites-available/ **	
	** sudo nano website1.com **
	
	you will get a similar looking block with comments , edit as follows for both .conf files : 
	
	**
  
  	<VirtualHost *:80>
	ServerAlias website(1/2).com
	ServerName www.website(1/2).com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/website(1/2).com
	ErrorLog ${APACHE_LOG_DIR}/website(1/2)/error.log
        CustomLog ${APACHE_LOG_DIR}/website(1/2)/access.log combined
	</VirtualHost>

	**
	Save and close the editor ( CTRL+X ).

	Now navigate to 
	** cd /etc/apache2 **
	And edit the apache config file by typing
	** sudo nano apache2.conf
	
	Find the <Directory /> blocks and copy the one thats look like this 
	** 
	  <Directory /var/www/>
          Options Indexes FollowSymLinks
          AllowOverride None
          Require all granted
	  </Directory>
	**

	 And paste it underneith the one you just copied And edit it to be the following 
	** 
	  <Directory /var/www/website(1/2).com/>
          Options Indexes FollowSymLinks
          AllowOverride None
          Require all granted
	  </Directory>
	**
	  

	
  4.5 Enable the virtual hosts :
	in the terminal  
	** sudo a2ensite website1 && sudo a2ensite website2 **
	** sudo service apache2 restart **  

	++ if you have an error of " the requested url / was not found on this server. " then i solved this by editing the document root of 		  	000-default.conf from 
	  	** /var/www/html/** 
	  	to 
	  	**/var/www/ ** 



  4.6(Local) Enable Local host Alias :

	TODO 

  4.6(Remote) 
	
	You should point your Domain name to your ip address followed by the coresponding folder for the domain. for example the DNS settings for 		website1.com should point to XXX.XXX.XXX.XXX/website1.com/html. you should find a tutorial to do this from your domain name provider.


	 If you visit your website domain name any you see the Index.html page you made for the wesite ,  your apache virtual hosts should be working, 		 now time to install Mysql and php. 

  5 Instaling MySql and PHP 5.6 : 

     5.1 Mysql:
	In terminal type 
	** sudo apt-get install mysql-server ** 
	Set your password that you want for the Mysql root user.

	After instilation, enter and follow the instilation instructions.
	** sudo mysql_secure_installation **
	And then run the command 
	** sudo mysql_install_db **

     5.2 PHP 5.6 :	
	To install php and some modules needed in the terminsal type 
	** sudo apt-get install libapache2-mod-php5 php5 php-pear php5-xcache php5-mysql php5-curl php5-gd **
	** sudo service apache2 restart **
	
	++ to test if php has been installed replace the file index.html made in step 4.2 with index.php containing the following 
		** <?php 
		   phpinfo();
		   ?>
		**


	Your php should be setup and you should see the information and settings for php if you visit website(1/2).com now to install PHPMyAdmin

  6 PHPMyAdmin Setup : 
	
    6.1 Install phpmyadmin :
	Navigate to /var/www/website(1/2).com in the terminal and type 
	** sudo apt-get install phpmyadmin **
	When it asks "Please choose the web server that should be automatically configured to run phpMyAdmin." select %%apache2%%.
	When asked to configure the database select %%yes&&
	Enter the password you set for the mysql database in step 5.1
	Now enter a password to use for the phpmyadmin admin login.

    6.2 Include phpmyadmin apache2.conf file : 
	After instilation naviage to /etc/apache2/ and edit the apache2.conf file by typing in the the terminal  
	** sudo nano apache2.conf **
	And add the follwing line to the bottom of the file 
	** Include /etc/phpmyadmin/apache.conf ** 
	Save and close the editor ( CTRL+X ) .
	
    6.3 Login to Phpmyadmin :
	Now restart the apache2 server by typing 
	** sudo service apache2 restart **

	You should now have phpmyadmin login when you visit (your ip ) xxx.xxx.xxx.xxx/phpmyadmin , Your credentials should be 
	User= root
	Password = the one you set in step 6.1


  7 Mounting files from NAS server :
	In the terminal type                   Nas Server Ip 
	** sudo mount -t cifs -o user=<user> //xxx.xxx.xxx.xxx/FooBar/Website(1/2) /var/www/website(1/2) **






