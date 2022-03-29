Backup script
==========


::

	# cat backupzimbra.sh
	#!/bin/bash
	rsync -avHK --exclude 'data.mdb' --delete /opt/zimbra/ /root/zimbra/
	su - zimbra -c '/opt/zimbra/common/bin/mdb_copy /opt/zimbra/data/ldap/mdb/db/ /opt/zimbra/backup/'
	exit


