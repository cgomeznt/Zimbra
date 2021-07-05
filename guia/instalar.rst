Instalar Zimbra Mail Server
===========================

Zimbra Collaboration Suite (ZCS) es una plataforma colaborativa de código abierto para servidores de correo electrónico, desarrollada en dos ediciones, **Open Source edition** (gratuita) y **Network Edition** (de pago), que brinda servicios como LDAP, SMTP, POP e IMAP, cliente de correo web, calendario, tareas, antivirus, antispam y otros.

Requerimientos
++++++++++++++++

Un servidor DNS externo con registros válidos A y MX para apuntar a la dirección IP de su servidor de correo Zimbra.

Una instalación limpia de CentOS sin ningún servidor de correo, bases de datos, LDAP, DNS o Http en funcionamiento.

Una dirección IP estática asignada a una interfaz de red.

Configuración del System Hostname.

Configuración del archivo /etc/hosts.

Configuración de un `SplitDNS <https://github.com/cgomeznt/Zimbra/blob/main/guia/SplitDNS.rst>`_.(Opcional) - http://docs.zimbra.com/docs/os/6.0.10/single_server_install/quick_start.1.05.html

Deshabilitar el Selinux y firewalld.


Si tienes una instalación de Zimbra y quiere Desinstalar::

	./install.sh -u
	Removing /opt/zimbra
	Removing zimbra crontab entry...done.
	Cleaning up zimbra init scripts...done.
	Cleaning up /etc/security/limits.conf...done.

	Finished removing Zimbra Collaboration Server.



Si en el proceso de instalación vemos este error, leer `SplitDNS <https://github.com/cgomeznt/Zimbra/blob/main/guia/SplitDNS.rst>`_.::

	DNS ERROR - none of the MX records for e-deus.cf
	resolve to this host


Adquirimos un dominio publico
++++++++++++++++++++++++++++++

En https://my.freenom.com adquirimos el Dominio y en realizamos las siguientes configuraciones:

**Records**

**Name	Type	TTL	Target**	

.	A	300	201.209.158.195

MAIL	A	3600	201.209.158.195

WWW	A	300	201.209.158.195

.	MX	3600	mail.e-deus.cf	Priority:5

.	TXT	3600	v=spf1 a:mail.e-deus.cf ip4:201.209.158.195/23 -all

NOTA: 201.209.158.195 es la IP Publica.

Dirección IP estática asignada a una interfaz de red
++++++++++++++++++++++++++++
Esta parte la dejamos sobre entendida que usted debe asignar una dirección IP Estática a uno de los adaptadores de red. En este ejemplo utilizaremos el adaptador ens32 con la IP Estática 192.168.1.121, IP Privada.


Editamos nuestro registro de HOSTS
+++++++++++++++++++++++++++++++++
::

	echo "192.168.1.121	mail.e-deus.cf mail" >> /etc/hosts

Configuración del System Hostname
+++++++++++++++++++++++++++
Vamos a configurar el nombre del equipo y preferiblemente que se llame igual como en el registro MX::

	hostnamectl set-hostname mail

Verificar configuración de DNS para Zimbra
++++++++++++++++++++++++++++++++

La configuración del DNS Publico se realizo gracias a https://my.freenom.com, sino tuviéramos que instalar nuestro Bind9 y configurarlo como `DNS_Publico <https://github.com/cgomeznt/DNS/blob/master/guia/dns-publico-CentOS7.rst>`_.

En este laboratorio se realizo una configuración de `SplitDNS <https://github.com/cgomeznt/Zimbra/blob/main/guia/SplitDNS.rst>`_.::


	dig e-deus.cf

	; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.5 <<>> e-deus.cf
	;; global options: +cmd
	;; Got answer:
	;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 14556
	;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

	;; OPT PSEUDOSECTION:
	; EDNS: version: 0, flags:; udp: 1280
	;; QUESTION SECTION:
	;e-deus.cf.			IN	A

	;; ANSWER SECTION:
	e-deus.cf.		300	IN	A	201.209.158.195

	;; Query time: 404 msec
	;; SERVER: 127.0.0.1#53(127.0.0.1)
	;; WHEN: dom jul 04 17:34:32 EDT 2021
	;; MSG SIZE  rcvd: 5
	::

Consultamos el MX::

	dig e-deus.cf MX

	; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.5 <<>> e-deus.cf MX
	;; global options: +cmd
	;; Got answer:
	;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 50094
	;; flags: qr aa rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2

	;; OPT PSEUDOSECTION:
	; EDNS: version: 0, flags:; udp: 4096
	;; QUESTION SECTION:
	;e-deus.cf.			IN	MX

	;; ANSWER SECTION:
	e-deus.cf.		0	IN	MX	5 mail.e-deus.cf.

	;; ADDITIONAL SECTION:
	mail.e-deus.cf.		0	IN	A	192.168.1.121

	;; Query time: 0 msec
	;; SERVER: 127.0.0.1#53(127.0.0.1)
	;; WHEN: dom jul 04 17:35:05 EDT 2021
	;; MSG SIZE  rcvd: 84

Consultamos el any, es decir todos los registros::

	dig e-deus.cf any

	; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.5 <<>> e-deus.cf any
	;; global options: +cmd
	;; Got answer:
	;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43897
	;; flags: qr aa rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2

	;; OPT PSEUDOSECTION:
	; EDNS: version: 0, flags:; udp: 4096
	;; QUESTION SECTION:
	;e-deus.cf.			IN	ANY

	;; ANSWER SECTION:
	e-deus.cf.		0	IN	MX	5 mail.e-deus.cf.

	;; ADDITIONAL SECTION:
	mail.e-deus.cf.		0	IN	A	192.168.1.121

	;; Query time: 1 msec
	;; SERVER: 127.0.0.1#53(127.0.0.1)
	;; WHEN: dom jul 04 17:35:41 EDT 2021
	;; MSG SIZE  rcvd: 84

Por defecto en los CentOS viene **Postfix**, lo detenemos y lo desinstalamos::

	systemctl stop postfix
	rpm -qa | grep postfix
	rpm -e postfix-2.10.1-9.el7.x86_64

Instalamos paquetes requeridos por Zimbra
++++++++++++++++++
::

	# yum install -y libidn gmp perl perl-core ntpl nmap sudo sysstat sqlite libaio libstdc++ wget unzip


Descargamos el Zimbra
++++++++++++++++++

**For RHEL/CentOS 8**::

	# wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_3953.RHEL8_64.20200629025823.tgz

**For RHEL/CentOS 7** Este es el caso que se utilizara en este laboratorio::

	# wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz

**For RHEL/CentOS 6**::

	# wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_3869.RHEL6_64.

Descomprimimos el paquete instalador
+++++++++++++++++++++++++++++++++++
::

	tar xvzf zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz 

Instalamos Zimbra
+++++++++++++

	cd zcs-8.8.15_GA_3869.RHEL7_64.20190918004220

	# ./install.sh 

	Operations logged to /tmp/install.log.ZTiE17ry
	Checking for existing installation...
	    zimbra-drive...NOT FOUND
	    zimbra-imapd...NOT FOUND
	    zimbra-patch...NOT FOUND
	    zimbra-mta-patch...NOT FOUND
	    zimbra-proxy-patch...NOT FOUND
	    zimbra-license-tools...NOT FOUND
	    zimbra-license-extension...NOT FOUND
	    zimbra-network-store...NOT FOUND
	    zimbra-network-modules-ng...NOT FOUND
	    zimbra-chat...NOT FOUND
	    zimbra-talk...NOT FOUND
	    zimbra-ldap...NOT FOUND
	    zimbra-logger...NOT FOUND
	    zimbra-mta...NOT FOUND
	    zimbra-dnscache...NOT FOUND
	    zimbra-snmp...NOT FOUND
	    zimbra-store...NOT FOUND
	    zimbra-apache...NOT FOUND
	    zimbra-spell...NOT FOUND
	    zimbra-convertd...NOT FOUND
	    zimbra-memcached...NOT FOUND
	    zimbra-proxy...NOT FOUND
	    zimbra-archiving...NOT FOUND
	    zimbra-core...NOT FOUND


	----------------------------------------------------------------------
	PLEASE READ THIS AGREEMENT CAREFULLY BEFORE USING THE SOFTWARE.
	SYNACOR, INC. ("SYNACOR") WILL ONLY LICENSE THIS SOFTWARE TO YOU IF YOU
	FIRST ACCEPT THE TERMS OF THIS AGREEMENT. BY DOWNLOADING OR INSTALLING
	THE SOFTWARE, OR USING THE PRODUCT, YOU ARE CONSENTING TO BE BOUND BY
	THIS AGREEMENT. IF YOU DO NOT AGREE TO ALL OF THE TERMS OF THIS
	AGREEMENT, THEN DO NOT DOWNLOAD, INSTALL OR USE THE PRODUCT.

	License Terms for this Zimbra Collaboration Suite Software:
	https://www.zimbra.com/license/zimbra-public-eula-2-6.html
	----------------------------------------------------------------------



	Do you agree with the terms of the software license agreement? [N] Y





	Use Zimbra's package repository [Y] 

	Importing Zimbra GPG key

	Configuring package repository

	Checking for installable packages

	Found zimbra-core (local)
	Found zimbra-ldap (local)
	Found zimbra-logger (local)
	Found zimbra-mta (local)
	Found zimbra-dnscache (local)
	Found zimbra-snmp (local)
	Found zimbra-store (local)
	Found zimbra-apache (local)
	Found zimbra-spell (local)
	Found zimbra-memcached (repo)
	Found zimbra-proxy (local)
	Found zimbra-drive (repo)
	Found zimbra-imapd (local)
	Found zimbra-patch (repo)
	Found zimbra-mta-patch (repo)
	Found zimbra-proxy-patch (repo)


	Select the packages to install

	Install zimbra-ldap [Y] Y

	Install zimbra-logger [Y] Y

	Install zimbra-mta [Y] Y

	Install zimbra-dnscache [Y] N

	Install zimbra-snmp [Y] Y

	Install zimbra-store [Y] Y

	Install zimbra-apache [Y] Y

	Install zimbra-spell [Y] Y

	Install zimbra-memcached [Y] Y

	Install zimbra-proxy [Y] Y

	Install zimbra-drive [Y] Y

	Install zimbra-imapd (BETA - for evaluation only) [N] N

	Install zimbra-chat [Y] Y
	Checking required space for zimbra-core
	Checking space for zimbra-store
	Checking required packages for zimbra-store
	zimbra-store package check complete.

	Installing:
	    zimbra-core
	    zimbra-ldap
	    zimbra-logger
	    zimbra-mta
	    zimbra-snmp
	    zimbra-store
	    zimbra-apache
	    zimbra-spell
	    zimbra-memcached
	    zimbra-proxy
	    zimbra-drive
	    zimbra-patch
	    zimbra-mta-patch
	    zimbra-proxy-patch
	    zimbra-chat

	The system will be modified.  Continue? [N] Y

	Beginning Installation - see /tmp/install.log.ZTiE17ry for details...

		                  zimbra-core-components will be downloaded and installed.
		                    zimbra-timezone-data will be installed.
		                  zimbra-common-core-jar will be installed.
		                 zimbra-common-mbox-conf will be installed.
		           zimbra-common-mbox-conf-attrs will be installed.
		            zimbra-common-mbox-conf-msgs will be installed.
		          zimbra-common-mbox-conf-rights will be installed.
		                   zimbra-common-mbox-db will be installed.
		                 zimbra-common-mbox-docs will be installed.
		           zimbra-common-mbox-native-lib will be installed.
		                 zimbra-common-core-libs will be installed.
		                             zimbra-core will be installed.
		                  zimbra-ldap-components will be downloaded and installed.
		                             zimbra-ldap will be installed.
		                           zimbra-logger will be installed.
		                   zimbra-mta-components will be downloaded and installed.
		                              zimbra-mta will be installed.
		                  zimbra-snmp-components will be downloaded and installed.
		                             zimbra-snmp will be installed.
		                 zimbra-store-components will be downloaded and installed.
		               zimbra-jetty-distribution will be downloaded and installed.
		                        zimbra-mbox-conf will be installed.
		                         zimbra-mbox-war will be installed.
		                     zimbra-mbox-service will be installed.
		               zimbra-mbox-webclient-war will be installed.
		           zimbra-mbox-admin-console-war will be installed.
		                  zimbra-mbox-store-libs will be installed.
		                            zimbra-store will be installed.
		                zimbra-apache-components will be downloaded and installed.
		                           zimbra-apache will be installed.
		                 zimbra-spell-components will be downloaded and installed.
		                            zimbra-spell will be installed.
		                        zimbra-memcached will be downloaded and installed.
		                 zimbra-proxy-components will be downloaded and installed.
		                            zimbra-proxy will be installed.
		                            zimbra-drive will be downloaded and installed (later).
		                            zimbra-patch will be downloaded and installed (later).
		                        zimbra-mta-patch will be downloaded and installed (later).
		                      zimbra-proxy-patch will be downloaded and installed (later).
		                             zimbra-chat will be downloaded and installed (later).

	Downloading packages (10):
	   zimbra-core-components
	   zimbra-ldap-components
	   zimbra-mta-components
	   zimbra-snmp-components
	   zimbra-store-components
	   zimbra-jetty-distribution
	   zimbra-apache-components
	   zimbra-spell-components
	   zimbra-memcached
	   zimbra-proxy-components
	      ...done

	Removing /opt/zimbra
	Removing zimbra crontab entry...done.
	Cleaning up zimbra init scripts...done.
	Cleaning up /etc/security/limits.conf...done.

	Finished removing Zimbra Collaboration Server.


	Installing repo packages (10):
	   zimbra-core-components
	   zimbra-ldap-components
	   zimbra-mta-components
	   zimbra-snmp-components
	   zimbra-store-components
	   zimbra-jetty-distribution
	   zimbra-apache-components
	   zimbra-spell-components
	   zimbra-memcached
	   zimbra-proxy-components
	      ...done

	Installing local packages (25):
	   zimbra-timezone-data
	   zimbra-common-core-jar
	   zimbra-common-mbox-conf
	   zimbra-common-mbox-conf-attrs
	   zimbra-common-mbox-conf-msgs
	   zimbra-common-mbox-conf-rights
	   zimbra-common-mbox-db
	   zimbra-common-mbox-docs
	   zimbra-common-mbox-native-lib
	   zimbra-common-core-libs
	   zimbra-core
	   zimbra-ldap
	   zimbra-logger
	   zimbra-mta
	   zimbra-snmp
	   zimbra-mbox-conf
	   zimbra-mbox-war
	   zimbra-mbox-service
	   zimbra-mbox-webclient-war
	   zimbra-mbox-admin-console-war
	   zimbra-mbox-store-libs
	   zimbra-store
	   zimbra-apache
	   zimbra-spell
	   zimbra-proxy
	      ...done

	Installing extra packages (5):
	   zimbra-drive
	   zimbra-patch
	   zimbra-mta-patch
	   zimbra-proxy-patch
	   zimbra-chat
	      ...done

	Running Post Installation Configuration:
	Operations logged to /tmp/zmsetup.20210630-220055.log
	Installing LDAP configuration database...done.
	Setting defaults...zmserverips: unable to execute: /sbin/ip



	DNS ERROR resolving MX for mail.e-deus.cf
	It is suggested that the domain name have an MX record configured in DNS
	Change domain name? [Yes] 
	Create domain: [mail.e-deus.cf] e-deus.cf
		MX: mail.e-deus.cf (201.210.54.31)



	It is suggested that the MX record resolve to this host
	Re-Enter domain name? [Yes] No
	done.
	Checking for port conflicts

	Main menu

	   1) Common Configuration:                                                  
	   2) zimbra-ldap:                             Enabled                       
	   3) zimbra-logger:                           Enabled                       
	   4) zimbra-mta:                              Enabled                       
	   5) zimbra-snmp:                             Enabled                       
	   6) zimbra-store:                            Enabled                       
		+Create Admin User:                    yes                           
		+Admin user to create:                 admin@e-deus.cf               
	******* +Admin Password                        UNSET                         
		+Anti-virus quarantine user:           virus-quarantine.jr0litgdw@e-deus.cf
		+Enable automated spam training:       yes                           
		+Spam training user:                   spam.hbpifavdo0@e-deus.cf     
		+Non-spam(Ham) training user:          ham.pm0uywnjj@e-deus.cf       
		+SMTP host:                            mail.e-deus.cf                
		+Web server HTTP port:                 8080                          
		+Web server HTTPS port:                8443                          
		+Web server mode:                      https                         
		+IMAP server port:                     7143                          
		+IMAP server SSL port:                 7993                          
		+POP server port:                      7110                          
		+POP server SSL port:                  7995                          
		+Use spell check server:               yes                           
		+Spell server URL:                     http://mail.e-deus.cf:7780/aspell.php
		+Enable version update checks:         TRUE                          
		+Enable version update notifications:  TRUE                          
		+Version update notification email:    admin@e-deus.cf               
		+Version update source email:          admin@e-deus.cf               
		+Install mailstore (service webapp):   yes                           
		+Install UI (zimbra,zimbraAdmin webapps): yes                           

	   7) zimbra-spell:                            Enabled                       
	   8) zimbra-proxy:                            Enabled                       
	   9) Default Class of Service Configuration:                                
	   s) Save config to file                                                    
	   x) Expand menu                                                            
	   q) Quit                                    

	Address unconfigured (**) items  (? - help) 6


	Store configuration

	   1) Status:                                  Enabled                       
	   2) Create Admin User:                       yes                           
	   3) Admin user to create:                    admin@e-deus.cf               
	** 4) Admin Password                           UNSET                         
	   5) Anti-virus quarantine user:              virus-quarantine.jr0litgdw@e-deus.cf
	   6) Enable automated spam training:          yes                           
	   7) Spam training user:                      spam.hbpifavdo0@e-deus.cf     
	   8) Non-spam(Ham) training user:             ham.pm0uywnjj@e-deus.cf       
	   9) SMTP host:                               mail.e-deus.cf                
	  10) Web server HTTP port:                    8080                          
	  11) Web server HTTPS port:                   8443                          
	  12) Web server mode:                         https                         
	  13) IMAP server port:                        7143                          
	  14) IMAP server SSL port:                    7993                          
	  15) POP server port:                         7110                          
	  16) POP server SSL port:                     7995                          
	  17) Use spell check server:                  yes                           
	  18) Spell server URL:                        http://mail.e-deus.cf:7780/aspell.php
	  19) Enable version update checks:            TRUE                          
	  20) Enable version update notifications:     TRUE                          
	  21) Version update notification email:       admin@e-deus.cf               
	  22) Version update source email:             admin@e-deus.cf               
	  23) Install mailstore (service webapp):      yes                           
	  24) Install UI (zimbra,zimbraAdmin webapps): yes                           

	Select, or 'r' for previous menu [r] 4

	Password for admin@e-deus.cf (min 6 characters): [NZBsVCRy] r00tme

	Store configuration

	   1) Status:                                  Enabled                       
	   2) Create Admin User:                       yes                           
	   3) Admin user to create:                    admin@e-deus.cf               
	   4) Admin Password                           set                           
	   5) Anti-virus quarantine user:              virus-quarantine.jr0litgdw@e-deus.cf
	   6) Enable automated spam training:          yes                           
	   7) Spam training user:                      spam.hbpifavdo0@e-deus.cf     
	   8) Non-spam(Ham) training user:             ham.pm0uywnjj@e-deus.cf       
	   9) SMTP host:                               mail.e-deus.cf                
	  10) Web server HTTP port:                    8080                          
	  11) Web server HTTPS port:                   8443                          
	  12) Web server mode:                         https                         
	  13) IMAP server port:                        7143                          
	  14) IMAP server SSL port:                    7993                          
	  15) POP server port:                         7110                          
	  16) POP server SSL port:                     7995                          
	  17) Use spell check server:                  yes                           
	  18) Spell server URL:                        http://mail.e-deus.cf:7780/aspell.php
	  19) Enable version update checks:            TRUE                          
	  20) Enable version update notifications:     TRUE                          
	  21) Version update notification email:       admin@e-deus.cf               
	  22) Version update source email:             admin@e-deus.cf               
	  23) Install mailstore (service webapp):      yes                           
	  24) Install UI (zimbra,zimbraAdmin webapps): yes                           

	Select, or 'r' for previous menu [r] r

	Main menu

	   1) Common Configuration:                                                  
	   2) zimbra-ldap:                             Enabled                       
	   3) zimbra-logger:                           Enabled                       
	   4) zimbra-mta:                              Enabled                       
	   5) zimbra-snmp:                             Enabled                       
	   6) zimbra-store:                            Enabled                       
	   7) zimbra-spell:                            Enabled                       
	   8) zimbra-proxy:                            Enabled                       
	   9) Default Class of Service Configuration:                                
	   s) Save config to file                                                    
	   x) Expand menu                                                            
	   q) Quit                                    

	*** CONFIGURATION COMPLETE - press 'a' to apply
	Select from menu, or press 'a' to apply config (? - help) a
	Save configuration data to a file? [Yes] 
	Save config in file: [/opt/zimbra/config.7254] 
	Saving config in /opt/zimbra/config.7254...done.
	The system will be modified - continue? [No] Yes
	Operations logged to /tmp/zmsetup.20210630-220055.log
	Setting local config values...done.
	Initializing core config...Setting up CA...done.
	Deploying CA to /opt/zimbra/conf/ca ...done.
	Creating SSL zimbra-store certificate...done.
	Creating new zimbra-ldap SSL certificate...done.
	Creating new zimbra-mta SSL certificate...done.
	Creating new zimbra-proxy SSL certificate...done.
	Installing mailboxd SSL certificates...done.
	Installing MTA SSL certificates...done.
	Installing LDAP SSL certificate...done.
	Installing Proxy SSL certificate...done.
	Initializing ldap...done.
	Setting replication password...done.
	Setting Postfix password...done.
	Setting amavis password...done.
	Setting nginx password...done.
	Setting BES searcher password...done.
	Creating server entry for mail.e-deus.cf...done.
	Setting Zimbra IP Mode...done.
	Saving CA in ldap...done.
	Saving SSL Certificate in ldap...done.
	Setting spell check URL...done.
	Setting service ports on mail.e-deus.cf...done.
	Setting zimbraFeatureTasksEnabled=TRUE...done.
	Setting zimbraFeatureBriefcasesEnabled=TRUE...done.
	Checking current setting of zimbraReverseProxyAvailableLookupTargets
	Querying LDAP for other mailstores
	Searching LDAP for reverseProxyLookupTargets...done.
	Adding mail.e-deus.cf to zimbraReverseProxyAvailableLookupTargets
	Updating zimbraLDAPSchemaVersion to version '1557224584'
	Setting TimeZone Preference...done.
	Disabling strict server name enforcement on mail.e-deus.cf...done.
	Initializing mta config...done.
	Setting services on mail.e-deus.cf...done.
	Adding mail.e-deus.cf to zimbraMailHostPool in default COS...done.
	Creating domain e-deus.cf...done.
	Setting default domain name...done.
	Creating domain e-deus.cf...already exists.
	Creating admin account admin@e-deus.cf...done.
	Creating root alias...done.
	Creating postmaster alias...done.
	Creating user spam.hbpifavdo0@e-deus.cf...done.
	Creating user ham.pm0uywnjj@e-deus.cf...done.
	Creating user virus-quarantine.jr0litgdw@e-deus.cf...done.
	Setting spam training and Anti-virus quarantine accounts...done.
	Initializing store sql database...done.
	Setting zimbraSmtpHostname for mail.e-deus.cf...done.
	Configuring SNMP...done.
	Setting up syslog.conf...Failed
	Starting servers...done.
	Installing common zimlets...
		com_zimbra_adminversioncheck...done.
		com_zimbra_bulkprovision...done.
		com_zimbra_attachcontacts...done.
		com_zimbra_srchhighlighter...done.
		com_zimbra_cert_manager...done.
		com_zimbra_tooltip...done.
		com_zextras_chat_open...done.
		com_zimbra_phone...done.
		com_zimbra_mailarchive...done.
		com_zimbra_webex...done.
		com_zimbra_attachmail...done.
		com_zextras_drive_open...done.
		com_zimbra_clientuploader...done.
		com_zimbra_ymemoticons...done.
		com_zimbra_date...done.
		com_zimbra_viewmail...done.
		com_zimbra_url...done.
		com_zimbra_proxy_config...done.
		com_zimbra_email...done.
	Finished installing common zimlets.
	Restarting mailboxd...done.
	Creating galsync account for default domain...done.

	You have the option of notifying Zimbra of your installation.
	This helps us to track the uptake of the Zimbra Collaboration Server.
	The only information that will be transmitted is:
		The VERSION of zcs installed (8.8.15_GA_3869_RHEL7_64)
		The ADMIN EMAIL ADDRESS created (admin@e-deus.cf)

	Notify Zimbra of your installation? [Yes] No
	Notification skipped
	Checking if the NG started running...done. 
	Setting up zimbra crontab...done.


	Moving /tmp/zmsetup.20210630-220055.log to /opt/zimbra/log


	Configuration complete - press return to exit 


Verificamos los puertos abiertos
+++++++++++++++++++++++++++++++
::

	# netstat -nat | grep -i listen
	tcp        0      0 0.0.0.0:7071            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7072            0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:23232         0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7073            0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:23233         0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:993             0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:995             0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:7171          0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7780            0.0.0.0:*               LISTEN     
	tcp        0      0 172.17.0.3:389          0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:5222            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7110            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7143            0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10024         0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10025         0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10026         0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:7306          0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10027         0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:587             0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:11211           0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10028         0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10029         0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10030         0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:3310          0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:110             0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:143             0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:10032         0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7025            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:465             0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:8465          0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:5269            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7993            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:25              0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:7995            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:8443            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN     
	tcp6       0      0 :::11211                :::*                    LISTEN  



