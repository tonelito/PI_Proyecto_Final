# Conexión válida a SRV1
En la laptop 2 (cmd): `ping 192.168.0.20`

En Ubuntu (terminal):
```
ip -4 addr
resolvectl query www.servidor1.com
```
Debe de tener una IP desde la `.26` y resolver `www.servidor1.com` a `192.168.0.20`
# Punto 1: Servidor de Virtualización
1. **Configuración de red de la Laptop 2** (`ipconfig` en cmd): Ethernet con **192.168.0.4**.
![](Imagenes/Captura-59.png)
2. **VirtualBox** con **SRV1** y **CLI-LINUX** en la lista, ambas _Corriendo_.
![](Imagenes/Captura-60.png)
3. **Configuración → Sistema** de SRV1: RAM y CPUs asignadas.
![](Imagenes/Captura-61.png)
4. **Configuración → Red** de SRV1: **Adaptador puente** sobre la tarjeta ASIS USB to Gigabit Ethernet Family Adapter.
![](Imagenes/Captura-62.png)
5. **Configuración → Red** de CLI-LINUX: Adaptador puente.
![](Imagenes/Captura-63.png)
6. **cmd en la Laptop 2** con `ping 192.168.0.20` respondiendo (el host llega a la VM).+
![](Imagenes/Captura-64.png)

---
# Punto 2: Servidor de Usuarios
## Parte 1
_Nombre e IP_

7. **Administrador del servidor → Servidor local**: nombre **SRV1**, dominio **servidor1.com**, IP 192.168.0.20. (ahora)
![](Imagenes/Captura-65.png)
8. **Propiedades IPv4** de SRV1 con la IP fija. (ahora)
![](Imagenes/Captura-66.png)

---
_DNS y Active Directory (2.1 y 2.2)_

9. **Administrador del servidor → Panel**: los roles **AD DS**, **DNS**, **DHCP**, **IIS** listados. (ahora)
![](Imagenes/Captura-67.png)
10. **Consola DNS**: zona directa **servidor1.com** con los registros `www`, `mail`, `ftp`, `srv1` y el **MX**. (ahora)
![](Imagenes/Captura-68.png)
11. **Consola DNS**: zona inversa **0.168.192.in-addr.arpa**. (ahora)
![](Imagenes/Captura-69.png)
12. **cmd en SRV1**: `nslookup www.servidor1.com` y `nslookup 192.168.0.20` respondiendo bien. (ahora)
![](Imagenes/Captura-70.png)

---
_Usuarios (2.3)_

13. **Usuarios y equipos de AD → Users**: usuario1, usuario2, privado, correo1, correo2. (ahora)
![](Imagenes/Captura-71.png)
14. **Propiedades de usuario1 → pestaña Cuenta**: inicio de sesión `usuario1@servidor1.com` y “La contraseña nunca expira”. (ahora)
![](Imagenes/Captura-72.png)
15. **Default Domain Policy → Directiva de contraseñas**: complejidad deshabilitada y longitud mínima 0. (ahora)
![](Imagenes/Captura-73.png)
16. **cmd**: `net user /domain` con la lista de usuarios. (ahora)
![](Imagenes/Captura-74.png)

---
_Política de grupo (2.4)_

17. **Administración de directivas de grupo**: `Politica Global USAC` vinculada a servidor1.com.
![](Imagenes/Captura-75.png)
18. **Editor**: “Papel tapiz del escritorio” habilitada con la ruta del logo.
![](Imagenes/Captura-76.png)
19. **Editor**: la asignación de unidad **P:** a CarpetaPública.
![](Imagenes/Captura-77.png)

20. **Editor $\rightarrow$ Configuración de usuario $\rightarrow$ Preferencias $\rightarrow$ Configuración de Windows $\rightarrow$ Registro**: elemento que pone el título "Política de Grupo" en Internet Explorer.
![313](Imagenes/Captura-88.png)

IE11 casi no muestra la barra de título, así que el texto se ve al pasar el mouse sobre su ícono en la barra de tareas, o con **Alt+Tab**

![215](Imagenes/Captura-89.png)

---
_DHCP (2.5 a 2.7)_

20. **Consola DHCP**: ámbito **Red Proyecto** activo, con su flecha verde. (ahora)
![](Imagenes/Captura-78.png)
21. **Conjunto de direcciones**: el rango **192.168.0.1 a .100** y la **exclusión .1 a .25**. (ahora)
![](Imagenes/Captura-79.png)
22. **Opciones de ámbito**: enrutador 192.168.0.1, DNS 192.168.0.20 y dominio servidor1.com. (ahora)
![](Imagenes/Captura-80.png)
23. **Concesiones de direcciones**: CLI-LINUX con su IP (.29). Con la Laptop 1 encendida aparecerá también CLI-WIN. (ahora)
![](Imagenes/Captura-81.png)

---
**Punto 2: CLI-LINUX (restricción f)**

24. **Terminal**: `ip -4 addr` mostrando la IP recibida por DHCP. Es la evidencia de “se asignan IPs dinámicamente a los clientes”.
![](Imagenes/Captura-82.png)
25. **Terminal**: `realm list` con `configured: kerberos-member`.
![](Imagenes/Captura-83.png)
26. **Terminal**: `id usuario1@servidor1.com` y `id usuario2@servidor1.com`.
![](Imagenes/Captura-84.png)
27. **Pantalla de inicio de Ubuntu** con `usuario1@servidor1.com` escrito.
![](Imagenes/Captura-85.png)
28. **Escritorio de Ubuntu** con sesión de usuario1, y una terminal con `whoami`.
![](Imagenes/Captura-86.png)
29. **SRV1 → Usuarios y equipos de AD → Computers**: **CLI-LINUX** aparece (y CLI-WIN, con la Laptop 1 encendida). (ahora)
![](Imagenes/Captura-87.png)

---
# Punto 3: Recursos Compartidos
## Parte 1: Instalación de IIS
**En SRV1 (asistente de Agregar roles y características):**
1. **Roles de servidor**, con **Servidor web (IIS)** marcado.
![](Imagenes/Captura-11.png)
2. **Servicios de rol**, con lo que agregaron a mano. Necesitarán 2 o 3 capturas, bajando en la lista:
    - Características HTTP comunes → **Redirección HTTP**.
![](Imagenes/Captura-12.png)
    - Seguridad → **Autenticación básica** y **Autenticación de Windows**.
![](Imagenes/Captura-13.png)
    - Servidor FTP → **Servicio FTP**.
![](Imagenes/Captura-14.png)
3. **Administrador de IIS** (Herramientas → Administrador de Internet Information Services) con **Sitios → Default Web Site** visible.
![](Imagenes/Captura-15.png)

**En Ubuntu (prueba):**

6. **Firefox** con `http://www.servidor1.com` y la página azul de bienvenida de IIS. Demuestra que IIS funciona y que se entra por nombre de dominio, no por IP (restricción e).
![](Imagenes/Captura-16.png)

---
## Parte 2: SRV1
1. Carpeta Pública $\rightarrow$ Uso compartido avanzado $\rightarrow$ Permisos: Todos con **Control Total**.
![](Imagenes/Captura-1.png)
2. Carpeta Pública $\rightarrow$ pestaña Seguridad: Usuarios del dominio con **Modificar**
![](Imagenes/Captura-2.png)
3. CarpetaPrivada $\rightarrow$ Uso compartido avanzado $\rightarrow$ Permisos solo `privado` con **Control Total**.
![](Imagenes/Captura-3.png)
4. CarpetaPrivada $\rightarrow$ Seguridad $\rightarrow$ Opciones avanzadas: la lista con `privado`, `IIS_IUSRS` y sin **Usuarios**.
![](Imagenes/Captura-4.png)
5. **cmd con `net share`**: muestra los dos recursos compartidos.
![](Imagenes/Captura-5.png)

---
## Parte 3: UBUNTU (Pruebas)
1. **Prueba 1**: usuario1 sube, lista y borra en CarpetaPública.
![](Imagenes/Captura-6.png)
2. **Prueba 2**: usuario1 recibe `NT_STATUS_ACCESS_DENIED` en CarpetaPrivada.
![](Imagenes/Captura-7.png)
3. **Prueba 3**: privado sube, lista y borra en CarpetaPrivada.
![](Imagenes/Captura-8.png)
4. _(Opcional, se ve mejor)_: **Archivos → Redes** → `smb://srv1.servidor1.com/CarpetaPublica`, con la carpeta abierta en el explorador de Ubuntu.
	1. Para la Carpeta Publica **Archivos → Redes** → `smb://srv1.servidor1.com/CarpetaPublica`
![](Imagenes/Captura-9.png)
	2. Para la Carpeta Privada ingresando con privado, `smb://SERVIDOR1;privado@srv1.servidor1.com/CarpetaPrivada`
![](Imagenes/Captura-10.png)
	3. Para la Carpeta Privada ingresando con usuario1. (deberia de mostar un error de permiso pero no lo muestra)

# Punto 4: Servidor de Correo Electrónico
## Instalación (SRV1)
1. Pantalla final del instalador de hMailServer, con la instalación completa.
![](Imagenes/Captura-40.png)

---
## Configuración de hMailServer (SRV1)
2. **Domains**, con `servidor1.com` creado.
![](Imagenes/Captura-42.png)
3. **Cuenta correo1 → pestaña Active Directory**, con “Active Directory account” marcado, dominio `SERVIDOR1` y usuario `correo1`. **Es la más importante.**

![](Imagenes/Captura-43.png)

4. **Accounts**, con la lista de `correo1@servidor1.com` y `correo2@servidor1.com`.
![](Imagenes/Captura-44.png)
5. **Settings → Protocols**, con SMTP y POP3 marcados.
![](Imagenes/Captura-45.png)
6. **SMTP → Delivery of e-mail**, con el host name `mail.servidor1.com`.
![](Imagenes/Captura-47.png)
7. **cmd** con la regla del firewall (puertos 25 y 110) y su respuesta “Aceptar”.
	`netsh advfirewall firewall add rule name="Correo hMail" dir=in action=allow protocol=TCP localport=25,110`
![](Imagenes/Captura-46.png)

---
## Thunderbird (CLI-LINUX)

8. **Configuración manual** de correo1: POP3 en el 110, SMTP en el 25, servidor `mail.servidor1.com`.
![](Imagenes/Captura-48.png)
![](Imagenes/Captura-49.png)
![](Imagenes/Captura-50.png)
![](Imagenes/Captura-51.png)
![](Imagenes/Captura-52.png)
![](Imagenes/Captura-53.png)
![](Imagenes/Captura-54.png)
9. Thunderbird con **las dos cuentas** (correo1 y correo2) en el panel izquierdo.
![](Imagenes/Captura-55.png)

---
## Prueba de envío
10. **Redacción** del correo de correo1 hacia `correo2@servidor1.com`.
![](Imagenes/Captura-56.png)
11. **Bandeja de correo2** con el correo recibido.
![](Imagenes/Captura-57.png)
12. _(Opcional)_ La **respuesta** de correo2 recibida en correo1.
![](Imagenes/Captura-58.png)

---
# Punto 6: Servidor Web
## Publicweb

1. **Bloc de notas** con el `index.html` de publicweb, mostrando el contenido.
![](Imagenes/Captura-17.png)
2. **Administrador de IIS → Agregar directorio virtual**, con alias `publicweb` y su ruta física.
![](Imagenes/Captura-18.png)
3. **Firefox (Ubuntu)** en `http://www.servidor1.com/publicweb` mostrando “Carpeta Pública”.
![](Imagenes/Captura-19.png)

---
## privadaweb

4. **Agregar sitio web** con `SitioPrivado` y el **puerto 1080** visible.
![](Imagenes/Captura-20.png)
5. **Directorio virtual** `privadaweb` dentro de SitioPrivado, con su ruta física.
![](Imagenes/Captura-21.png)
6. **Autenticación** de privadaweb: Anónima _Deshabilitado_, Básica _Habilitado_.
![](Imagenes/Captura-22.png)
7. **cmd** con el comando del firewall para el puerto 1080 y su respuesta “Aceptar”.
	`netsh advfirewall firewall add rule name="IIS 1080" dir=in action=allow protocol=TCP localport=1080`
![](Imagenes/Captura-23.png)
8. **Firefox** en `http://www.servidor1.com:1080/privadaweb` con la **ventana que pide usuario y contraseña**.
![](Imagenes/Captura-24.png)
9. **Firefox** mostrando “Carpeta Privada” después de entrar con `privado`.
![](Imagenes/Captura-25.png)
10. **Firefox** rechazando a `usuario1` (error 401 o vuelve a pedir la contraseña).
*Simplemente limpia los datos ingresados no muestra ningún error*

---
## correo
11. **Agregar directorio virtual** con alias `correo` y la ruta `C:\inetpub\correo-redir`.
![](Imagenes/Captura-26.png)
12. **Redirección HTTP** de correo, con la casilla marcada y la URL de destino.
![](Imagenes/Captura-27.png)
13. **Firefox**: la barra de direcciones ya cambiada a la URL del webmail tras entrar a `http://www.servidor1.com/correo`.
Antes de ingresar a la dirección
![](Imagenes/Captura-28.png)
Resultado de ingresar
![](Imagenes/Captura-29.png)

---
## General

14. **Administrador de IIS**, con el árbol completo expandido: Default Web Site con `publicweb` y `correo`, y SitioPrivado con `privadaweb`.
![](Imagenes/Captura-30.png)

# Punto 8: Servidor FTP
## Configuración en SRV1 (asistente “Agregar sitio FTP”)
1. **Información del sitio**: nombre `FTP Servidor1` y ruta `C:\Recursos\CarpetaPublica`.
![](Imagenes/Captura-31.png)
2. **Enlace y SSL**: IP `192.168.0.20`, puerto `21`, “Sin SSL”.
![](Imagenes/Captura-32.png)
3. **Autenticación y autorización**: Básica marcada, usuarios especificados `usuario1, usuario2`, permisos Lectura y Escritura. **Es la más importante**: demuestra los incisos 8.2 y 8.3.
![](Imagenes/Captura-33.png)
4. **Administrador de IIS** con el sitio `FTP Servidor1` visible en Sitios, y a la derecha en **Administrar sitio FTP** “Iniciado” debería de aparecer en gris, lo cual indica que ya esta iniciado.
![](Imagenes/Captura-34.png)
5. **cmd** con `net stop ftpsvc` y `net start ftpsvc` ejecutados correctamente.
![](Imagenes/Captura-35.png)

---
## Pruebas en Ubuntu
6. **usuario1** conectado por FTP: inicio de sesión correcto (`230 User logged in`), `put ftp.txt` y `ls` mostrando el archivo.
![](Imagenes/Captura-36.png)
7. **usuario2**, la misma prueba. Demuestra que funciona para ambos usuarios.
![](Imagenes/Captura-37.png)
8. _(Opcional, se ve mejor)_: **FileZilla** conectado a `ftp.servidor1.com`, con los archivos del servidor a la derecha.
![](Imagenes/Captura-38.png)

---
## Comprobación final en SRV1
9. **Explorador** en `C:\Recursos\CarpetaPublica` mostrando el archivo `ftp.txt` que subieron desde Ubuntu. Demuestra que el FTP escribe en la carpeta pública.
![](Imagenes/Captura-39.png)