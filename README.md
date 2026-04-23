# Apuntes de las clases de GIT-GITHUB con el Auxigood Isac
## Apuntes Git dia Lunes 20/04/2026
El auxi nos conto del chisme de Linus Torvals, creo Linux para hacerle frente a programas de paga que cobraban por gestionar versiones de un proyecto
Comandos de configuracion:

```
git config --global user.name "TU_USER_NAME" //Debes indetificarte al momento de hacer commits
git config --global user.email "TU_EMAIL"   //Debes indetificarte al momento de hacer commits
git config --global core.autocrlf true  //Sirve para corregir saltos de linea en Windos/Linux/Mac
```
## Apuntes Git dia Martes 21/04/2026
Vimos los estados de Git, working, stage y confirmado.
comando para agregar archivos al stage
```
git add . //el punto agrega todo los arhcivos modificados al stage, opcionalkmente puede seleccionar solo el archivo que quieras agregar al stage escribes la ruta inicial del archivo lo completas con tabs.
```
Comando para resturar del stage:
```
git restore --staged . //el punto restaura todos los archivos del stage, opcionalkmente puede seleccionar solo el archivo que quieras restauras escribes la ruta inicial del archivo lo completas con tabs.
```
Comando para hacer commit(Confirmar los cambios)
```
git commit -m "Mensaje descriptivo de los cambios que hiciste, se recomienda usar prefijos add,fix,feat,etc." //Para ver prefijos revisar PDF, o buscar en internet GIT FLOW
```
Vimos el .gitignore, en este archivo pones los archivos, directorios o tipos de archivos en especifico(Segun su extension, ejemplo: .java, .py, .Ts...etc) para que git los ignore, osea que no seguira los cambios que se hagan en esos archivos del .gitignore.

Tambien vimos los commits con cuerpo, se usan para cuando queramos poner una descripcion mas larga de los cambios que hicimos
comando:
```
git commit //Se abre el editor configurado Nano o VIM.
```
En el editor:
- La primera línea debe ser el título del commit usando un prefijo (ej: feat, fix, docs, etc.).
- Luego se deja una línea en blanco.
- A partir de la tercera línea se escribe el cuerpo del commit, donde se explica con más detalle qué cambios se hicieron y por qué.