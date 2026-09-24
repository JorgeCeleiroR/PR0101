# PR0101
Trabajo PR0101 de Luis por JCR

=== PASO 1 - ACTUALIZAR LOS REPOSITORIOS ===
PRIMER COMANDO: sudo apt update
SEGUNDO COMANDO: sudo apt upgrade -y

=== PASO 2 - INSTALAR DEPENDENCIAS ===
COMANDO USADO: sudo apt install software-properties-common apt-transport-https -y
"software-properties-common" te da herramientas como add-apt-repository.
"apt-transport-https"  permite que apt pueda descargar paquetes desde repositorios HTTPS (necesario para el de Webmin).

FOTO: <img width="1125" height="582" alt="image" src="https://github.com/user-attachments/assets/eb8d40e2-066f-4f2f-8958-01dbb3d40d9a" />

=== PASO 3 - AÑADIR EL REPOSITORIO DE WEBMIN ===
PRIMER COMANDO: curl -o webmin-setup-repo.sh https://raw.githubusercontent.com/webmin/webmin/master/webmin-setup-repo.sh
COMPROBANTE: <img width="980" height="66" alt="image" src="https://github.com/user-attachments/assets/b44b0e28-9798-42be-912d-5b76ba364031" />

SEGUNDO COMANDO: sudo sh webmin-setup-repo.sh
COMPROBANTE: <img width="508" height="242" alt="image" src="https://github.com/user-attachments/assets/ac145117-dff1-48d1-851d-0d3e204583fa" />

Esto lo que va a hacer es descargar el script oficial de Webmin. 
Además de añadir su clave GPG y su repositorio a tu sistema

=== PASO 4 - INSTALAR WEBMIN === 
Ahora instalamos Webmin con los paquetes recomendados.
PRIMER COMANDO: sudo apt-get install --install-recommends webmin -y
Esto tardara un par de minutos.
COMPROBANTE: <img width="642" height="795" alt="image" src="https://github.com/user-attachments/assets/e3a8da61-a2cd-4350-bf53-6c446ec70da1" />

=== PASO 5 - CONFIGURAR CORTAFUEGOS === 
PRIMER COMANDO: sudo ufw status 
Con este primer comando veremos el estado, si esta "inactive" o nos lista reglas activa. En mi caso esta "inactive".
SEGUNDO COMANDO: sudo ufw allow ssh
Este segundo comando permite el puerto 22 (SSH), para no bloquear el acceso remoto.
TERCER COMANDO: sudo ufw allow 10000/tcp
Con este comando abrimos el puerto 10000, que es el que usa WEBMIN
CUARTO COMANDO: sudo ufw enable
Y aqui finalmente activamos el firewall, nos pedira con este comando confirmacion ( escribimos y ).
COMPROBANTE: <img width="442" height="150" alt="image" src="https://github.com/user-attachments/assets/eaa165fd-ba99-47ca-9b52-40ca3c1126ba" />

COMANDO COMPROBAR: sudo ufw status
Podemos usar este comando para comprobar que todo salio bien en los pasos anteriores, es opcional.
COMPROBANTE: <img width="465" height="175" alt="image" src="https://github.com/user-attachments/assets/62cbd3db-4e9b-4cbe-8d2d-0d8bbd35eafd" />


=== PASO 6 - EJECUTAR WEBMIN ===
PRIMER COMANDO: sudo systemctl status webmin
( En caso de no estar activo usamos los siguientes comandos: sudo systemctl start webmin y sudo systemctl enable webmin )
En mi caso ya esta activo y corriendo. COMPROBANTE: <img width="1183" height="377" alt="image" src="https://github.com/user-attachments/assets/c8b4932a-a74b-4638-a96b-25f36b5369c7" />

=== PASO 7 - ASIGNAR CONTRASEÑA DE ROOT PARA WEBMIN === 
PRIMER COMANDO: sudo /usr/share/webmin/changepass.pl /etc/webmin root PasswordAlumno
Esto le da a Webmin una contraseña propia para el usuario root, independiente de si la cuenta root del sistema está bloqueada o no.
COMPROBANTE: <img width="767" height="33" alt="image" src="https://github.com/user-attachments/assets/0ce2f8df-4db9-40dd-92f7-19f594845502" />

=== ULTIMO PASO - ACCEDER A WEBMIN DESDE EL NAVEGADOR === 
PRIMER COMANDO: ip a -> para conocer nuestra IP de la maquina.
COMPROBANTE: <img width="882" height="372" alt="image" src="https://github.com/user-attachments/assets/ed02b077-edcb-4712-ba16-439350ec168c" />
En mi caso tengo dos interfaces de red:
 - enp0s3 → 10.0.2.15 (es la red NAT de VirtualBox, normalmente no accesible directamente desde tu navegador del host)
 - enp0s8 → 192.168.0.2 (es una red Host-Only, esta sí debería ser accesible desde tu máquina física)

Para abrir Webmin usamos el siguiente enlace: https://192.168.0.2:10000. Este enlace es personal variara depende de la ip y el puerto de cada uno.

COMPROBANTE: <img width="1917" height="906" alt="image" src="https://github.com/user-attachments/assets/792e18da-ddc1-41f0-84cd-c490eecb1322" />


--- VAMOS A AUTOMATIZARLO ---
=== PASO 1 - CREAMOS EL FICHERO ===
PRIMER COMANDO: mkdir -p scripts
SEGUNDO COMANDO: nano scripts/.env
Dentro del nano añadiremos lo siguiente: WEBMIN_ROOT_PASSWORD="PasswordAlumno", WEBMIN_PORT=10000 y WEBMIN_USER: "root".
También podemos añadir el puerto SSH que hemos usado, en mi caso ha sido el SSH_PORT: 22. 
Para salir del nano guardas con Ctrl+O, Enter, y sales con Ctrl+X.

=== PASO 2 - CREAMOS EL .sh ===
PRIMER COMANDO: nano scripts/webmin-install.sh
COMPROBANTE: <img width="955" height="751" alt="image" src="https://github.com/user-attachments/assets/71f3b4ea-44a9-4971-bcb7-0273e5853cac" />



