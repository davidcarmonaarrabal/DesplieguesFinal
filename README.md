# Resumen y guía de Despliegues de cara al último examen.

## Resumen teórico.

### 1. LDAP.

#### 1.1. ¿Qué es un servidor LDAP?
Protocolo que almacena credenciales, permite acceso de usuarios a redes sin crearlos en el SO. Puede almacenar información extra de usuarios como contacto o certificados, usado en **Docker**, **OpenVPN** y servidores de archivos. Común en *Intranets* pero se puede ver en redes públicas.

#### 1.2. Diferencia de Autenticación y Autorización.
La **autenticación** es un proceso de *identificación*, la **autorización** es el *nivel de permisos y acceso que tiene un usuario autenticado*.

#### 1.3. Componentes de una estructura LDAP.
***Componentes de estructura:***
* **Directorios**. Organizan información en un *árbol jerárquico*.
* **Entradas**. Unidades básicas de información identificadas por su *DN (Distinguished Name)*.
* **Atributos**. Describen la entrada con nombre y valores.
* **LDIF**. Formato de texto usado para LDAP.

***Elementos clave de estructura:***
* **dn**. Identificador de entrada.
* **cn**. Nombre común en el directorio.
* **dc**. Define la jerarquía del dominio.
* **objectClass**. Define el tipo de entrada y atributos que permite.

**LDAP** almacena un *subárbol de entradas*, organizando y gestionando la información de *autorización y autenticación* eficientemente.

#### 1.4. Principales clases de organización.
Para estructurar una organización en **LDAP**, se usan diferentes clases según el elemento a crear (se mostrará ahora la clase y el elemento obligatorio de esta):
* **Unidad organizativa**. `organizationalUnit`, `ou`.
* **Grupo**. `posixGroup`, `cn` y `gidNumber`.
* **Usuario**.
  * `inetOrgPerson`, `cn` y `sn`.
  * `posixAccount`, `cn`, `uid`, `uidNumber`, `gidNumber` y `homeDirectory`.
  * `shadowAccount`, `uid`.
 
Estas clases estructuran los usuarios, grupos y unidades organizativas.

### 2. APACHE.

#### 2.1. Lado servidor y lado cliente.
El **servidor** maneja peticiones, procesa información y devuelve respuestas, mientras que el **cliente** realiza solucitudes al servidor y recibe respuestas, mostrando el contenido en navegadores.

#### 2.2. Diferencia entre HTTP y HTTPS.
**HTTP** tiene puerto 80, no cifra información, no requiere certificado, puede ser modificado por terceros, no garantiza la identidad del servidor, es bueno para sitios sin información preciada.

**HTTPS** en cambio usa puerto 443, usa SSL/TLS para cifrar, requiere certificado difital, protege la manipulación de datos, garantiza identidad y es bueno para transacciones seguras, es decir, es *una versión segura de HTTP*.

#### 2.3. Propietario de un proceso.
Hereda los *permisos y propiedades* del usuario que lo ejecuta, *Apache* debe iniciarse con permisos de **root** para acceder a los puertos, para luego cambiar de usuario por seguridad y ejecutarse con menos privilegios *(suele usar "nobody" o "apache")*.

Los permisos de lectura se ponen con `chmod a+r archivo`, los de ejecución con `chmod a+x directorio`.

#### 2.4. ¿Qué es un módulo? ¿Qué uso tiene?
Es una *extensión* que añade funcionalidades a un servidor sin modificar el núcleo, pueden ser **estáticos** (en el binario de Apache) o **dinámicos** (cargados en tiempo de ejecución con LoadModule).

Los módulos permiten:
* **Optimizar rendimiento**, al cargar solo funciones necesarias.
* **Ampliar funcionalidades**, como soporte HTTPS o ejecución de scripts PHP.
* **Gestión de configuraciones específicas**.

Algunos módulos son:
* mod_ssl.
* mod_ldap.
* mod_php.
* mod_proxy.
* mod_security.
* mod_userdir, permite que cada usuario tenga su propio directorio web personal.

Para ver los módulos activos usamos `httpd -M` y para instalar en Fedora uno usamos `dnf install modulo`, para desactivarlo se comenta la línea `LoadModule` en `/etc/httpd/conf.modules.d/`.

#### 2.5. ¿Qué son los ficheros log?
Registran información de la actividad del servidor, permiten mejorar rendimiento, ver errores, etc. Se guardan en `/var/log/httpd` y los principales son `access.log` y `error.log`.

El **LogLevel** define el nivel de detalle de estos, teniendo *emerg*, *crit*, *error*, *warn*, *info* y *debug*.

#### 2.6. ¿Qué es PidFile? 
Guardda el identificador del proceso principal de Apache, útil para reiniciarlo o detenerlo (`PidFile run/httpd.pid`).

#### 2.7. Tipos de directores virtuales, enlace simbólico y alias.
* **Alias**. Mapea una URL a un directorio físico fuera del servidor, es fácil de configurar pero menos flexible para cosas complejas.
* **Enlace simbólico**. Crea un acceso directo a un directorio, muy flexible pero requiere configurar permisos y directivas.

Alias es más fácil pero enlace simbólico más flexible.

#### 2.8. Configuración de host virtuales.
Permiten alojar varios sitios en un solo servidor, configurados en `httpd.conf` o `/etc/httpd/conf.d`, por ejemplo:

```
<VirtualHost *:80>
 DocumentRoot "/var/www/example.com"
 ServerName example.com
</VirtualHost>
```

**Alias** mapea una URL a un directorio fuera de la raíz con `Alias /dvirtual "/var/pagina2/html"` y **enlace simbólico** crea un acceso directo con `ln -s /var/pagina2/html /var/www/html/dvirtual`.

#### 2.9. Diferencia entre host virtuales y host con los nombres.
Los **virtuales basados en IP** necesitan IP única, menos eficientes pero no requieren cambios en la configuración de DNS.

Los **basados en nombres** utilizan una sola IP para varios dominios, requiere la directiva `NameVirtualHost` y que el DNS resuelva bien los nombres de dominio.

#### 2.10. Host virtuales basados en puertos.
Se configura Apache para que responda a varios puertos en la misma dirección IP, dependiendo del puerto que se accede, se daun contenido diferente. Su problema es que se deben abrir puertos adicionales, puede haber problemas con cortafuegos.

