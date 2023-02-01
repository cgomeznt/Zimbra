Uninstall Zimbra Collaboration Suite
============================================

Para Uninstall, correr el script install con -u y entonces se eliminara el ZCS y se removera el ZCS tgz archivos del servidor.
1. Cambie al directorio en donde esta instalado zabbix.
2. Escriba ./install.sh -u.
3. Cuando se complete, tipee Yes.
The Zimbra servers are stopped, the existing packages, the webapp directories, and the /opt/zimbra directory are removed.
4. Delete the zcs directory, type rm -rf [zcsfilename].
5. Delete the zcs.tgz file, type rm -rf zcs.tgz.
6. Additional files may need to be delete. See the Zimbra Wiki Installation section on http://wiki.zimbra.com/index.php?title=Main_Page.
Note: For Mac, type cd /;/opt/zimbra/libexec/installer/install-mac.sh - u.
