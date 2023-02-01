

Crear:
  DNS Record - mail, este es el host que sera el que atienda el MX.
  
  MX Record	-	dominio.com.ve	-	mail.dominio.com.ve	-	190.114.9.23
  
  SPF Record	-	TXT "v=spf1 a:dominio.com.ve ip4:190.114.9.23/29 ~all"
  
  _DMARC	Record -	TXT "v=DMARC1; p=quarantine; rua=mailto:admin@dominio.com.ve"
  
  Reverse zones and PTR records	-	Crear una zona reversa ya crear el registro para el mail.dominio.com.ve
  
  
Utilizar estos comandos para el dignostico::

  dig dominio.com.ve
  
  dig dominio.com.ve MX
  
  dig dominio.com.ve txt
  
  dig _dmarc.dominio.com.ve txt
  
  
Tambien podemos utilizar estos link que nos ayuda a los dignosticos de forma visual:

  https://mxtoolbox.com/
  
  https://intodns.com/
  
  
