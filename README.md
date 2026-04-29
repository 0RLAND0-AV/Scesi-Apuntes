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

## Apuntes Git dia Miercoles 22/04/2026
Vimos github, creacion de cuentas en github, login creacion de repositorios remotos, conexion via ssh.
Comandos utiles:
Comando para crear clave ssh:
```
ssh-keygen -t ed25519 -C “tu-correo@email.com”
```
```
cat ~/.ssh/id_ed25519.pub
```

Copias el contenido del anteroir comando y te diriges a github donde te diriges a tu perfil → Settings y luego SSH y GPG Keys y luego “New SSH Key” (1) y pegas tu key, le das un nombre para tu PC y click en “Add SSH Key”. (2)

Comando para verificar que tu git local este conectado a tu cuenta github:
```
ssh -T git@github.com
```

Comando para conectar un repositorio local a un repositorio remoto(NUEVO)
```
git remote add origin git@github.com:TuUser/TuRepo.git

git branch -M main

git push -u origin main
```
Luego vimos los tipicos comandos git push, git pull, para especificar alguna rama se usa el "origin" depsues del push/pull


## Apuntes Git día Jueves 23/04/2026
PROMPT CHATGPT: Genera un resumen de esta informacion, primera persona, solo lo mas esencial, aqui tienes un ejemplo "Apuntes git dia Miercoles......"

Hoy vimos temas más avanzados: **git remote**, manejo de **múltiples cuentas con SSH**, configuraciones locales y **git checkout** (navegación en la historia).

---

### 🔹 Git Remote (repositorios remotos)

Sirve para gestionar a dónde se conecta tu repo local (como GitHub).

Comandos útiles:

```
git remote -v
```

Muestra las URLs configuradas

```
git remote add <apodo> "url"
```

Conecta tu repo local con uno remoto

```
git remote set-url <apodo> "url"
```

Cambia la URL del remoto

---

### 🔹 Múltiples cuentas con SSH

Si tienes más de una cuenta, necesitas **una llave SSH por cuenta**.

Crear nueva key:

```
ssh-keygen -t ed25519 -C "correo" -f ~/.ssh/id_nombre
```

Configurar archivo:

```
~/.ssh/config
```

Ejemplo:

```
# Cuenta principal
Host github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_ed25519

# Segunda cuenta
Host github-nombre
HostName github.com
User git
IdentityFile ~/.ssh/id_nombre
```

Verificar:

```
ssh -T git@github-nombre
```

---

### 🔹 Clonar usando el host correcto

IMPORTANTE: usar el alias configurado

```
git clone git@github-nombre:usuario/repositorio.git
```

---

### 🔹 Configuraciones locales

Se aplican solo al repo actual (no globales):

```
git config user.name "Tu nombre"
git config user.email "tu correo"
```

---

### 🔹 Git Checkout

Permite moverte entre ramas o commits.

```
git checkout <rama>
```

Cambiar de rama

```
git checkout <hash>
```

Ir a un commit antiguo

Usos:

* Ver código del pasado
* Recuperar archivos
* Probar cambios
* Cambiar de rama

---

### 🔹 Detached HEAD

Estado donde NO estás en una rama, sino en un commit.

⚠️ Si haces cambios aquí, se pueden perder

Solución:

```
git checkout -b nueva_rama
```

---

### 🔹 Ir y volver entre commits

```
git checkout <hash>
```

Ir al pasado

```
git checkout main
```

Volver a la rama actual

---

### 🔹 Buenas prácticas

* Hacer commit antes de moverte
* No trabajar mucho en detached HEAD
* Crear ramas para experimentar
* Mantener limpio el proyecto

---

## Apuntes Git día Lunes 27/04/2026
PROMPT CHATGPT: Genera un resumen de esta informacion, primera persona, solo lo mas esencial, aqui tienes un ejemplo "Apuntes git dia Jueves......"

Lunes se aprendio sobre ramas y Gitflow básico para trabajar de forma más ordenada en equipo.

Vimos cómo crear, listar y eliminar ramas usando:

```bash
git branch
git branch <rama>
git branch -D <rama>
```

También aprendimos a movernos entre ramas y crear ramas nuevas directamente con:

```bash
git checkout <rama>
git checkout -b <rama>
```

El auxi explicó la diferencia entre `git checkout` y `git switch`, donde `git switch` es más seguro y moderno para trabajar únicamente con ramas.

Finalmente vimos Gitflow básico y las ramas principales:

* `main`: contiene el código en producción.
* `develop`: rama donde se integran los cambios en desarrollo.
* `feature/*`: para desarrollar nuevas funcionalidades.
* `release/*`: para preparar nuevas versiones.
* `hotfix/*`: para arreglar errores urgentes en producción.

También aprendimos buenas prácticas para mantener el proyecto más organizado y fácil de trabajar en equipo.
