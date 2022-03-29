Habilitando relaying 
==================================


Permitir relaying desde maquinas remotas
+++++++++++++++++++++++++++++++++++++
Maquina remota con IP address 10.10.200.25::

	postconf mynetworks
	mynetworks = 127.0.0.0/8 10.10.130.0/24

Agregar la maquina remota::

	zmprov ms zimbra.example.com zimbraMtaMyNetworks '127.0.0.0/8 10.10.130.0/24 10.10.200.25/32'
	postfix reload

Permitir relaying desde redes remottas
+++++++++++++++++++++++++++++++++++++++++++
Redes remotas  192.168.1.x, con un netmask of 255.255.255.0)::

	postconf mynetworks
	mynetworks = 127.0.0.0/8 10.10.130.0/24

Agregar las redes remotas::

	zmprov ms zimbra.example.com zimbraMtaMyNetworks '127.0.0.0/8 10.10.130.0/24 192.168.1.0/24'
	postfix reload
