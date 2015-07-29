<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. Archivo de Lenguas Indígenas Nacionales del INALI</a>
<ul>
<li><a href="#sec-1-1">1.1. Requerimientos</a></li>
<li><a href="#sec-1-2">1.2. Estructura</a></li>
<li><a href="#sec-1-3">1.3. Instalación</a></li>
<li><a href="#sec-1-4">1.4. Post-instalación</a></li>
<li><a href="#sec-1-5">1.5. Ajustes de JVM</a>
<ul>
<li><a href="#sec-1-5-1">1.5.1. Parámetros de memoria</a></li>
<li><a href="#sec-1-5-2">1.5.2. Parámetros de procesamiento y Garbage Collector</a></li>
</ul>
</li>
<li><a href="#sec-1-6">1.6. Posibles problemas de instalación</a>
<ul>
<li><a href="#sec-1-6-1">1.6.1. Falla la compilación de mvn por inexistencia de ciertos directorios:</a></li>
<li><a href="#sec-1-6-2">1.6.2. Falla de compilación de mvn por alguna otra causa</a></li>
<li><a href="#sec-1-6-3">1.6.3. No se puede crear el usuario administrador</a></li>
<li><a href="#sec-1-6-4">1.6.4. No se pueden subir archivos al repositorio</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#sec-2">2. Uso básico del Sistema del Archivo de Lenguas Indígenas Nacionales</a>
<ul>
<li><a href="#sec-2-1">2.1. Panel Administrativo</a>
<ul>
<li><a href="#sec-2-1-1">2.1.1. Usuarios</a></li>
<li><a href="#sec-2-1-2">2.1.2. Grupos</a></li>
</ul>
</li>
<li><a href="#sec-2-2">2.2. Crear una familia, agrupación o variante lingüística</a></li>
<li><a href="#sec-2-3">2.3. Enviar un ítem</a></li>
<li><a href="#sec-2-4">2.4. Aceptar un ítem</a></li>
</ul>
</li>
</ul>
</div>
</div>

# Archivo de Lenguas Indígenas Nacionales del INALI<a id="sec-1" name="sec-1"></a>

-   La carpeta src contiene las fuentes de DSpace 5.1 con sus respectivas personalizaciones,
-   La carpeta config tiene los archivos para agregar las webapps a Tomcat 7,
-   Información más específica en [la documentacion oficial de DSpace](https://wiki.duraspace.org/display/DSDOC5x/DSpace%2B5.x%2BDocumentation).

## Requerimientos<a id="sec-1-1" name="sec-1-1"></a>

El sistema ALIN ha sido probado con el siguiente entorno:

-   GNU/Linux (Gentoo y Debian),
-   PostgreSQL 9.4,
-   Maven 3,
-   OpenJDK7 o Oracle JDK8,
-   Tomcat7 o Tomcat8.

## Estructura<a id="sec-1-2" name="sec-1-2"></a>

Esta guía  toma en  cuenta las rutas  dentro de  la carpeta **alin**  que contiene  los siguientes
archivos y directorios:

    drwxr-xr-x config
    -rw-r--r-- presentacion.pdf
    -rw-r--r-- presentacion.tex
    -rw-r--r-- README.md
    -rw-r--r-- README.org
    drwxr-xr-x src

La carpeta **src** contiene las fuentes del sistema ALIN, mientras que **config** tiene los archivos
de  configuración mínimos  de  las webapps  mínimas  necesarias para  que  ALIN funcione.  Estos
archivos XML pueden ser incluidos en Tomcat.

## Instalación<a id="sec-1-3" name="sec-1-3"></a>

El  archivo básico  de configuración  se  encuentra en  **src/build.properties** y  el archivo  de
configuración completo está en  **src/dspace/config/dspace.cfg**. La configuración de contraseñas,
cuenta de correo de sistema e instalación se hará en el primer archivo mencionado.

El sistema ALIN estará instalado predeterminadamente en la carpeta **/srv/dspace** bajo la base de
datos **dspace**.

    # Crear el usuario de sistema llamado dspace, dueño de la base de datos 
    useradd -m dspace
    # Crear el usuario dspace en PostgreSQL
    createuser --username=postgres --no-superuser --pwprompt dspace
    # Crear base de datos dspace
    createdb --username=postgres --owner=dspace --encoding=UNICODE dspace
    # Entrar en las fuentas de DSpace
    cd alin/src
    # Ajustar y verificar las opciones básicas de configuración
    vi build.properties
    # Como root crear la carpeta de instalación de DSpace
    mkdir /srv/dspace
    chown dspace /srv/dspace
    # Compilar DSpace como el usuario DSpace
    su - dspace
    cd alin/src/dspace
    mvn package
    # Instalar
    cd alin/src/dspace/target/dspace-installer
    ant fresh_install
    # Copiar los archivos de configuración a Tomcat
    # En Debian
    cp alin/config/* /etc/tomcat[7|8]/Catalina/localhost/
    # En Gentoo
    cp alin/config/* /etc/[perfil_tomcat]/Catalina/localhost/
    # En otras distribuciones localizar $CATALINA_BASE
    # Iniciar Tomcat y crear el usuario administrador
    /etc/init.d/tomcat start
    /srv/dspace/bin/dspace create-administrator

## Post-instalación<a id="sec-1-4" name="sec-1-4"></a>

Ingresar al sismema  como administrador. Ir al menú:  Administrativo>Registro>Metadatos

Ahí se verá una tabla, entrar en el esquema llamado **dc** generalmente con el ID 1.

Agregar los siguientes campos:

    Nombre del campo: dc.language.fam
    Nota de alcance: Family of the language

    Nombre del campo:  dc.language.grp
    Nota de alcance: Group of the language

## Ajustes de JVM<a id="sec-1-5" name="sec-1-5"></a>

Hay que establecer los  parámetros de JVM de modo que  se haga un uso óptimo de  la memoria y el
Garbage Collector con el procesador. Algunos  parámetros a revisar importantes para monitorear y
optimizar estos dos aspectos son:

    -Xms
    -Xmx
    -Xmns
    -Xmnx
    -Xgcthreads
    -verbose:sizes
    -verbose:gc
    -Xgcpolicy:gencon
    -Xcompactgc
    -Xalwaysclassgc
    -Xverbosegclog:/path/to/log/file

Es  posible  que  la validez  de  estos  parámetros  varie  dependiendo  de la  versión  de  JDK
utilizada. En caso de error, verificar las opciones válidas para el JDK elegido, ver `man java`.

### Parámetros de memoria<a id="sec-1-5-1" name="sec-1-5-1"></a>

Para optimizar  la JVM  es recomendable  modificar los valores  de **initialHeapSize**  (`-Xms`) y
**maximumHeapSize** (`-Xmx`) en la variable `JAVA_OPTS`.  Establecer ambos parámetros con el mismo
valor permite  evitar intentos de  reasignación de  memoria. Por lo  general, el valor  de estos
parámetros en un servidor dedicado debe ser 3/4 de la RAM total.

También es importante  establecer `-Xmxns` y `-Xmxnx` con  el mismo valor, que debe  ser 1/4 del
tamaño seleccionado para `-Xms`.

El parámetro `-Xlp` permite una paginación más larga en caso de ser necesario.

### Parámetros de procesamiento y Garbage Collector<a id="sec-1-5-2" name="sec-1-5-2"></a>

Optimizar el número de hilos para el Garbage Collector es importante. El parámetro `-Xgcthreads`
debe ser  igual al número  de núcleos  de procesador disponibles.   Por ejemplo, si  existen dos
procesadores de  doble núcleo, especifique `-Xgcthreads4`.   Si existen 4 procesadores  de doble
núcleo, o 2 procesadores de núcleo cuádruple, especifique `-Xgcthreads8`.

## Posibles problemas de instalación<a id="sec-1-6" name="sec-1-6"></a>

### Falla la compilación de mvn por inexistencia de ciertos directorios:<a id="sec-1-6-1" name="sec-1-6-1"></a>

    # mkdir -p alin/src/dspace/modules/{rdf,jspui,rest,sword,swordv2,solr,oai}/src/main/webapp

### Falla de compilación de mvn por alguna otra causa<a id="sec-1-6-2" name="sec-1-6-2"></a>

-   Verificar los permisos del usuario **dspace** sobre las carpetas **/srv/dspace** y **alin/src**,
-   Verificar la versión correcta de maven, usar `update-aternatives` si es un sistema Debian,
-   Verificar la versión de JDK, usar `update-aternatives` si es un sistema Debian,
-   Tratar de compilar como usuario **root** y después ajustar permisos en **/srv/dspace**.

### No se puede crear el usuario administrador<a id="sec-1-6-3" name="sec-1-6-3"></a>

El error se presenta de la siguiente manera:

    Exception: Error, no admin group (group 1) found
    java.lang.IllegalStateException: Error, no admin group (group 1) found
            at org.dspace.administer.CreateAdministrator.createAdministrator(CreateAdministrator.java:238)
            at org.dspace.administer.CreateAdministrator.negotiateAdministratorDetails(CreateAdministrator.java:206)
            at org.dspace.administer.CreateAdministrator.main(CreateAdministrator.java:82)
            at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
            at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
            at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
            at java.lang.reflect.Method.invoke(Method.java:497)
            at org.dspace.app.launcher.ScriptLauncher.runOneCommand(ScriptLauncher.java:225)
            at org.dspace.app.launcher.ScriptLauncher.main(ScriptLauncher.java:77)

Es provocado por una mala escritura de la base de datos al momento de la instalación, para solucionarlo:

1.  Detener Tomcat,
2.  Limpiar base de datos `/srv/dspace/bin/dspace database clean` **Esto borra por completo la base
    de datos**,
3.  Migrar la base de datos a la versión más reciente `/srv/dspace/bin/dspace database migrate`,
4.  Crear el usuario administrador,
5.  Iniciar Tomcat.

### No se pueden subir archivos al repositorio<a id="sec-1-6-4" name="sec-1-6-4"></a>

Este  es un  problema  de permisos,  primeramente,  las carpetas  **alin**  y **/srv/dspace**  deben
pertenecer   al   usuario   **dspace**   pero   tomcat  requiere   permisos   para   escribir   en
**/srv/dspace**. Esto puede arreglarse por medio de ACL:

    setfacl -R -d -m u:tomcat8:rwx dspace

Los permisos deberían ser como los siguientes:

    # file: dspace
    # owner: dspace
    # group: dspace
    user::rwx
    group::r-x
    other::r-x
    default:user::rwx
    default:user:tomcat8:rwx
    default:group::r-x
    default:mask::rwx
    default:other::r-x

# Uso básico del Sistema del Archivo de Lenguas Indígenas Nacionales<a id="sec-2" name="sec-2"></a>

[Página principal del sitio de ALIN](README/alin_principal.jpg)

## Panel Administrativo<a id="sec-2-1" name="sec-2-1"></a>

[Panel Administrativo](README/panel_administrativo.jpg)

El   panel   Administrativo   aparece   solamente    en   las   cuentas   con   privilegios   de
**Administración**. Aquí  se verán dos  de sus opciones más  importantes: Control de  acceso sobre
grupos y usuarios.

### Usuarios<a id="sec-2-1-1" name="sec-2-1-1"></a>

Para administrar usuarios se accede en el enlace de **Personas**.

Desde aquí hay tres posibles acciones:

-   Buscar usuarios: Por ID, nombre o correo electrónico,
-   Borrar usuarios:  Marcando la casilla situada  del lado izquierdo  de cada usuario a  borrar y
    luego presionando el botón correspondiente,
-   Crear usuarios:  Pulsando en el  enlace correspondiente a ésta  acción se solicitará  de forma
    obligatoria el correo electrónico y nombre completo del nuevo usuario.  Para activar la cuenta
    deberá marcar la casilla  **Puede acceder**, de otro modo el usuario no  podrá entrar al sistema
    con su cuenta.

Es importante  señalar que una vez  creada una cuenta de  usuario, el usuario debe  entrar en la
pantalla de acceso al sistema ALIN e iniciar  el proceso de **¿Olvidó su contraseña?** con el cual
recibirá un correo electrónico para elegir la contraseña de su cuenta.

Alternativamente, ALIN cuenta con un modo de línea  de comandos con el cual el administrador del
servidor puede  crear cuentas con una  contraseña predeterminada (no recomendado  por motivos de
seguridad y privacidad para el usuario) de la siguiente manera:

    /srv/dspace/bin/dspace user -a -g NOMBRE -s APELLIDO -m CORREO@ELECTRONICO -l es -p CONTRASEÑA

### Grupos<a id="sec-2-1-2" name="sec-2-1-2"></a>

Los  grupos son  la  forma de  organizar  usuarios  que tendrán  privilegios  comúnes. De  forma
predeterminada existen dos grupos en el sistema:

-   Anonymous. Con el ID 0.  Este grupo debe permanecer vacío y controla a  todo usuario que no ha
    iniciado sesión en el sistema ALIN,
-   Administrator. Con el ID 1. Este grupo  contiene a los usuarios con permisos de administración
    general sobre  todo el sistema ALIN.  Idealmente solo debe  existir un usuario dentro  de este
    grupo.

Para administrar  grupos se  accede en el  enlace de  **Grupos**. Ésta sección  es muy  similar al
apartado de **Personas** de la sección anterior.

Para la administración del sistema ALIN se crearon dos grupos nuevos:

-   Publicadores. Contiene  a los usuarios  que tienen  permiso de envío  de ítems nuevos  a todas
    las variantes lingüísticas del sistema,
-   Administradores. Encargados de crear y administrar todas agrupaciones lingüísticas y variantes
    lingüísticas dentro del sistema, además de aprobar los envíos de los publicadores.

## Crear una familia, agrupación o variante lingüística<a id="sec-2-2" name="sec-2-2"></a>

Estos tres grupos se crean  de forma similar y depende de la ubicación  de un usuario dentro del
sistema así como de los privilegios con los que cuenta en el mismo. 

Las familias son creadas por el administrador  general del sistema. Las agrupaciones son creadas
por  usuarios  que  pertenecen  al  grupo **Administradores**  con  la  opción  **Crear  agrupación
lingüística** que aparece en el menú **Contexto** cuando se está dentro de una familia lingüística.

Las variantes  son creadas dentro  de las  agrupaciones (preferentemente) aunque  también pueden
crearse directamente  dentro de  las familias.  Pueden crearlas los  usuarios que  pertenecen al
grupo  **Administradores**  con  la  opción  **Crear  variante  lingüística**  ubicada  en  el  menú
**Contexto**.

[Formulario para crear variante](README/crear_variante.jpg)

Para la creación de familias y agrupaciones se solicitan los siguientes datos:

-   Nombre. Obligatorio,
-   Descripción breve,
-   Texto introductorio,
-   Texto de copyright. Con información sobre las  personas que tienen derechos sobre los archivos
    incluidos o información sobre cómo citar las obras,
-   Novedades.

Además la creación de variantes solicita:

-   Licencia. Con información de derecho de cita y usos permitidos,
-   Origen. De la información incluida.

[Apartado para asignar roles](README/roles_vacio.jpg)

Una vez creada la agrupación o variante necesaria,  es oportuno ir al apartado **Asignar roles** y
asociar a un grupo o usuario a cada rol.

[Asignando roles 1](README/roles_admin.jpg)
[Asignando roles 2](README/roles_submit.jpg)
[Roles asignados](README/roles_lleno.jpg)

## Enviar un ítem<a id="sec-2-3" name="sec-2-3"></a>

Un Ítem es un conjunto de archivos con información lingüística pertinente que incluyen metadatos
con la  información necesaria para identificarlos.  De forma predeterminada los  usuarios dentro
del grupo **Publicadores** pueden enviar ítems nuevos al sistema para su posterior revisión.

La opción **Envíos** ubicada en el menú **Mi cuenta**  permite a un usuario revisar el estado de los
ítems que ha enviado así como enviar nuevos ítems.

Además de  los archivos pertinentes  para enviar  un ítem es  necesario contar con  la siguiente
información:

-   Autor. Obligatorio,
-   Informante,
-   Título. Obligatorio,
-   Otros títulos,
-   Lugar de Grabación. Obligatorio,
-   Fecha de creación. Obligatorio,
-   Fecha de publicación,
-   Editor,
-   Estándar de cita,
-   Serie/Reporte,
-   Identificadores,
-   Tipo de género discursivo o evento comunicativo,
-   Idioma de la investigación,
-   Familia, agrupación y variante lingüística,
-   Descripción general,
-   Patrocinadores. Personas o entidades que financiaron la investigación.

Cada campo está debidamente comentado en el  formulario de ingreso. Una vez terminado el proceso
de envío los archivos serán revisados y en su caso aceptados para su publicación.

## Aceptar un ítem<a id="sec-2-4" name="sec-2-4"></a>

El  proceso de  revisión de  un ítem  enviado  por un  usuario tiene  tres pasos  que deben  ser
realizados  por los  miembros del  grupo **Administradores**  o por  un usuario  con los  premisos
pertinentes en una variante lingüística determinada.

Los pasos son los  siguientes: La variante lingüística recibe un ítem.  Un administrador toma la
tarea de revisar  los archivos enviados, si el  ítem es aceptado, continua el paso  dos donde un
administrador verifica los metadatos ingresados en el  ítem, si el ítem es aceptado, continua al
tercer y  último paso donde un  administrador hace una  revisión general del ítem,  modifica los
permisos de los archivos incluidos y acepta su publicación.

[Flujo de trabajo](README/flujo.jpg)
