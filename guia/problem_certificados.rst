Certifcates problem with Zimbra
==============================

::

	zmprov


	ERROR: zclient.IO_ERROR (invoke sun.security.validator.ValidatorException: PKIX path validation failed: java.security.cert.CertPathValidatorException: timestamp check failed, server: localhost) (cause: javax.net.ssl.SSLHandshakeException sun.security.validator.ValidatorException: PKIX path validation failed: java.security.cert.CertPathValidatorException: timestamp check failed)

The way to solve this is doing “zmcontrol stop” and proceed as follows::

	1. Begin by generating a new Certificate Authority (CA).

 	/opt/zimbra/bin/zmcertmgr createca -new

2. Then generate a certificate signed by the CA that expires in 365 days::

 	/opt/zimbra/bin/zmcertmgr createcrt -new -days 365

3. Next deploy the certificate::

 	/opt/zimbra/bin/zmcertmgr deploycrt self

4. Next deploy the CA::

 	/opt/zimbra/bin/zmcertmgr deployca

5. To finish, verify the certificate was deployed to all the services::

 	/opt/zimbra/bin/zmcertmgr viewdeployedcrt

Finally you execute “zmcontrol start” and you are done, Enjoy.
