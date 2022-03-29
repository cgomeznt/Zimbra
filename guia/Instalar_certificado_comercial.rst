Instalar un certificado comercial
====================================

Deben tener el certificado y lo deben copiar en::

	/opt/zimbra/ssl/zimbra/commercial/

Verificar el certificado SSL con zimbra
+++++++++++++++++++++++++++++++++++

Como usuario de zimbra::

	su - zimbra

	/opt/zimbra/bin/zmcertmgr verifycrt comm privkey.pem cert.pem chain.pem
	** Verifying 'cert.pem' against 'privkey.pem'
	Certificate 'cert.pem' and private key 'privkey.pem' match.
	** Verifying 'cert.pem' against 'chain.pem'
	Valid certificate chain: cert.pem: OK


Deploy el certificado SSL con zimbra
+++++++++++++++++++++++++++++++++++

Como usuario de zimbra::

	$ /opt/zimbra/bin/zmcertmgr deploycrt comm cert.pem chain.pem

	** Verifying 'cert.pem' against '/opt/zimbra/ssl/zimbra/commercial/commercial.key'
	Certificate 'cert.pem' and private key '/opt/zimbra/ssl/zimbra/commercial/commercial.key' match.
	** Verifying 'cert.pem' against 'chain.pem'
	Valid certificate chain: cert.pem: OK
	** Copying 'cert.pem' to '/opt/zimbra/ssl/zimbra/commercial/commercial.crt'
	** Copying 'chain.pem' to '/opt/zimbra/ssl/zimbra/commercial/commercial_ca.crt'
	** Appending ca chain 'chain.pem' to '/opt/zimbra/ssl/zimbra/commercial/commercial.crt'
	** Importing cert '/opt/zimbra/ssl/zimbra/commercial/commercial_ca.crt' as 'zcs-user-commercial_ca' into cacerts '/opt/zimbra/common/lib/jvm/java/lib/security/cacerts'
	** NOTE: restart mailboxd to use the imported certificate.
	** Saving config key 'zimbraSSLCertificate' via zmprov modifyServer mail.e-deus.cf...failed (rc=1)
	** Installing imapd certificate '/opt/zimbra/conf/imapd.crt' and key '/opt/zimbra/conf/imapd.key'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.crt' to '/opt/zimbra/conf/imapd.crt'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.key' to '/opt/zimbra/conf/imapd.key'
	** Creating file '/opt/zimbra/ssl/zimbra/jetty.pkcs12'
	** Creating keystore '/opt/zimbra/conf/imapd.keystore'
	** Installing ldap certificate '/opt/zimbra/conf/slapd.crt' and key '/opt/zimbra/conf/slapd.key'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.crt' to '/opt/zimbra/conf/slapd.crt'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.key' to '/opt/zimbra/conf/slapd.key'
	** Creating file '/opt/zimbra/ssl/zimbra/jetty.pkcs12'
	** Creating keystore '/opt/zimbra/mailboxd/etc/keystore'
	** Installing mta certificate '/opt/zimbra/conf/smtpd.crt' and key '/opt/zimbra/conf/smtpd.key'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.crt' to '/opt/zimbra/conf/smtpd.crt'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.key' to '/opt/zimbra/conf/smtpd.key'
	** Installing proxy certificate '/opt/zimbra/conf/nginx.crt' and key '/opt/zimbra/conf/nginx.key'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.crt' to '/opt/zimbra/conf/nginx.crt'
	** Copying '/opt/zimbra/ssl/zimbra/commercial/commercial.key' to '/opt/zimbra/conf/nginx.key'
	** NOTE: restart services to use the new certificates.
	** Cleaning up 9 files from '/opt/zimbra/conf/ca'
	** Removing /opt/zimbra/conf/ca/ca.key
	** Removing /opt/zimbra/conf/ca/ca.pem
	** Removing /opt/zimbra/conf/ca/77927c8c.0
	** Removing /opt/zimbra/conf/ca/commercial_ca_1.crt
	** Removing /opt/zimbra/conf/ca/8d33f237.0
	** Removing /opt/zimbra/conf/ca/commercial_ca_2.crt
	** Removing /opt/zimbra/conf/ca/4042bcee.0
	** Removing /opt/zimbra/conf/ca/commercial_ca_3.crt
	** Removing /opt/zimbra/conf/ca/2e5ac55d.0
	** Copying CA to /opt/zimbra/conf/ca
	** Copying '/opt/zimbra/ssl/zimbra/ca/ca.key' to '/opt/zimbra/conf/ca/ca.key'
	** Copying '/opt/zimbra/ssl/zimbra/ca/ca.pem' to '/opt/zimbra/conf/ca/ca.pem'
	** Creating CA hash symlink '77927c8c.0' -> 'ca.pem'
	** Creating /opt/zimbra/conf/ca/commercial_ca_1.crt
	** Creating CA hash symlink '8d33f237.0' -> 'commercial_ca_1.crt'
	** Creating /opt/zimbra/conf/ca/commercial_ca_2.crt
	** Creating CA hash symlink '4042bcee.0' -> 'commercial_ca_2.crt'
	** Creating /opt/zimbra/conf/ca/commercial_ca_3.crt
	** Creating CA hash symlink '2e5ac55d.0' -> 'commercial_ca_3.crt'
	[zimbra@mail mail.e-deus.cf-0003]$ zmcontrol restart

Reiniciamos Zimbra
+++++++++++++++++++
::

	zmcontrol restart


Test el nuevo SSL Certificado con OpenSSL
++++++++++++++++++++++
::

	echo QUIT | openssl s_client -connect e-deus.cf:443 | openssl x509 -noout -text | less

