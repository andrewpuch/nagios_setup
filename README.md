NAGIOS SERVER 4.0.8
-------------------
`sudo su

apt-get update`

apt-get upgrade -y

apt-get dist-upgrade -y

apt-get autoremove -y

apt-get install build-essential libgd2-xpm-dev openssl libssl-dev apache2-utils apache2 php5 -y

useradd nagios

groupadd nagcmd

usermod -a -G nagcmd nagios

cd /root

curl -L -O http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.0.8.tar.gz

tar xvf nagios-*.tar.gz

cd nagios-4.0.8

./configure --with-nagios-group=nagios --with-command-group=nagcmd

make all

make install

make install-commandmode

make install-init

make install-config

/usr/bin/install -c -m 644 sample-config/httpd.conf /etc/apache2/sites-available/nagios.conf

curl -L -O http://nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz

tar xvf nagios-plugins-*.tar.gz

cd nagios-plugins-2.0.3

./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl

make

make install

nano /usr/local/nagios/etc/nagios.cfg

Remove the hashtag in front of this line. 
----------------------------------------

Before --

#cfg_dir=/usr/local/nagios/etc/servers

After --

cfg_dir=/usr/local/nagios/etc/servers

mkdir /usr/local/nagios/etc/servers

Add Your Email
--------------

nano /usr/local/nagios/etc/objects/contacts.cfg

a2enmod rewrite cgi

htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin

ln -s /etc/apache2/sites-available/nagios.conf /etc/apache2/sites-enabled/

service nagios start

service apache2 restart

ln -s /etc/init.d/nagios /etc/rcS.d/S99nagios

nano /usr/local/nagios/etc/servers/monitor1.cfg

define host {
        use                             linux-server
        host_name                       monitor1 
        alias                           My first monitoring server.
        address                         PRIVATE_IP_NAGIOS_SERVER
        max_check_attempts              5
        check_period                    24x7
        notification_interval           30
        notification_period             24x7
}

define service {
        use                             generic-service
        host_name                       monitor1
        service_description             PING
        check_command                   check_ping!100.0,20%!500.0,60%
}

service nagios reload

NAGIOS CLIENT MACHINE
---------------------
sudo su
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get autoremove -y
apt-get install nagios-plugins nagios-nrpe-server -y

nano /etc/nagios/nrpe.cfg

Edit
----
allowed_hosts=127.0.0.1 PRIVATE_IP_NAGIOS_SERVER

service nagios-nrpe-server restart
