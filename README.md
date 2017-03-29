# Buildout base para proyectos con Odoo y PostgreSQL
Odoo 10 en el base, PostgreSQL 9.6.1 y Supervisord 3.0
- Supervisor ejecuta PostgreSQL, más info http://supervisord.org/
- También ejecuta la instancia de PostgreSQL
- Si existe  un archivo frozen.cfg es el que se debeía usar ya que contiene las revisiones aprobadas
- PostgreSQL se compila y corre bajo el usuario user (no es necesario loguearse como root), se habilita al autentificación "trust" para conexiones locales. Más info en more http://www.postgresql.org/docs/9.3/static/auth-methods.html
- Existen plantillas para los archivo de configuración de Postgres que se pueden modificar para cada proyecto.


## Utilización
En caso de no haberse hecho antes en la máquina en la que se vaya a realizar, instalar las dependencias que marca Anybox
- Añadir el repo a /etc/apt/sources.list:
```
$ deb http://apt.anybox.fr/openerp common main
```
- Si se quiere añadir la firma. Esta a veces tarda mucho tiempo o incluso da time out. Es opcional meterlo
```
$ sudo apt-key adv --keyserver hkp://subkeys.pgp.net --recv-keys 0xE38CEB07
```
- Actualizar e instalar
```
$ sudo apt-get update
$ sudo apt-get install openerp-server-system-build-deps
```
- Para poder compilar e instalar postgres
```
$ sudo apt-get install libreadline-dev
```

Clonar repositorio
> git clone https://github.com/Comunitea/CMNT_SPEGC.git



- Crear un virtualenv dentro de la carpeta del respositorio. Esto podría ser opcional, obligatorio para desarrollo o servidor de pruebas, tal vez podríamos no hacerlo para un despliegue en producción. Si no está instalado, instalar el paquete de virtualenv. Es necesario tener la versión que se instala con easy_install o con pip, desinstalar el paquete python-virtualenv si fuera necesario e instalarlo con easy_install
```
$ sudo easy_install virtualenv
```

Dentro de la carpeta del repositorio
```
$ cd CMNT_SPEGC
```

```
$ virtualenv sandbox --no-setuptools
```
Executar bootstrap. 
```
$ sandbox/bin/python bootstrap.py
```
Se creará la siguiente estrucura de directorios 

```bash

odoo
├── bin
├── bootstrap.py
├── buildout.cfg
├── default.cfg
├── develop-eggs
├── downloads
├── eggs
├── etc
├── nfe_xml
├── parts
├── Scenario
├── specific-parts
├── src
├── upgrade.py
├── README.md
└── var

```
- Lanzar buildout (el -c [archivo_buildout] se usa cuando no tiene el nombre por defecto buildout.cfg)
```
 $ bin/buildout -c [archivo_buildout]
 ```

- Puede que de error, si intenta crear la BD y  no está arrancado supervisor todava. Hay que lanzar el supervisor y volver a hacer bin/buildout:
```
$ bin/supervisord
$ bin/buildout -c [archivo_buildout]
```

```
$ cd bin
$ ./upgrade_openerp
```
- odoo se lanza en el puerto 8069 (se pude configurar en otro)



## Configurar Odoo
Archivo de configuración: etc/odoo.cfg, si sequieren cambiar opciones en  odoo.cfg, no se debe editar el fichero,
si no añadirlas a la sección [odoo] del buildout.cfg
y establecer esas opciones .'add_option' = value, donde 'add_option'  y ejecutar buildout otra vez.

Por ejemplo: cambiar el nivel de logging de Odoo
```
'buildout.cfg'
...
[odoo]
options.log_handler = [':ERROR']
...
```

Si se quiere ejecutar más de una instancia de Odoo, se deben cambiar los puertos,
please change ports:
```
openerp_xmlrpc_port = 8069  (8069 default odoo)
openerp_xmlrpcs_port = 8071 (8071 default odoo)

postgres_port = 5434        (5432 default postgres)
```

# Contributors

## Creators

Rastislav Kober, http://www.kybi.sk


### Para "congelar" una versión de cada módulo y dependencia

Después de ejecutar con éxito el buildout, basta utilizar el siguiente comando:
```
$ bin/buildout -o odoo:freeze-to=odoo-freeze.cfg 
```
Se creará un archivo llamado `odoo-freeze.cfg` con las dependencias y revisionnes de cada mudulo. De este modo
tendremso el control de módulos y versiones usadas.

Para utilizar el `freeze` basta executar
```
$ bin/buildout -c odoo-freeze.cfg 
```
