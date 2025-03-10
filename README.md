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
