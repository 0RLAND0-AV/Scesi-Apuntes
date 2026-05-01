# Apuntes de Git & GitHub
### Clase con el Auxigood Isac — Abril/Mayo 2026

![Git](https://img.shields.io/badge/Git-2.x-F05032?style=flat-square&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)
![Estado](https://img.shields.io/badge/Estado-En%20curso-green?style=flat-square)

> Registro completo de comandos, conceptos y flujos de trabajo vistos en clase. Desde configuración inicial hasta Pull Requests y Gitflow profesional.

---

## Tabla de Contenidos

- [Lun 20/04 — Configuración inicial](#lunes-2004--configuración-inicial-de-git)
- [Mar 21/04 — Estados, Commits y .gitignore](#martes-2104--estados-commits-y-gitignore)
- [Mié 22/04 — GitHub y SSH](#miércoles-2204--github-y-conexión-ssh)
- [Jue 23/04 — Git Remote, múltiples SSH y Checkout](#jueves-2304--git-remote-múltiples-cuentas-ssh-y-checkout)
- [Lun 27/04 — Ramas y Gitflow](#lunes-2704--ramas-y-gitflow)
- [Mar 28/04 — Merge, Fetch, Pull y Push](#martes-2804--merge-fetch-pull-push-y-flujo-sin-pr)
- [Mié 29/04 — Pull Requests](#miércoles-2904--pull-requests-y-protección-de-ramas)
- [Jue 30/04 — Stash y Diff](#jueves-3004--git-stash-y-git-diff)
- [Cheatsheet general](#cheatsheet-general)

---

## Lunes 20/04 — Configuración inicial de Git

### Origen de Git y por qué existe

**Linus Torvalds** creó Git en 2005 como respuesta a la cancelación de la licencia gratuita de *BitKeeper*, el software de control de versiones que usaba el kernel de Linux. No quería depender de herramientas propietarias de pago, así que en dos semanas construyó su propio sistema: rápido, distribuido y libre.

> **Git ≠ GitHub.** Git es el sistema de control de versiones que corre en tu computadora. GitHub es una plataforma web que aloja repositorios Git remotos y agrega funciones de colaboración. Son cosas distintas.

---

### Comandos de configuración global

La configuración global se guarda en `~/.gitconfig` y aplica a todos tus repositorios.

```bash
# Identificarte para que tus commits muestren tu nombre
git config --global user.name  "TU_USER_NAME"
git config --global user.email "TU_EMAIL"

# Corregir saltos de línea entre Windows (CRLF) y Linux/Mac (LF)
git config --global core.autocrlf true   # En Windows
git config --global core.autocrlf input  # En Linux/Mac

# Ver toda tu configuración actual
git config --list
```

| Opción | Dónde aplica |
|--------|--------------|
| `--global` | Todos los repositorios del usuario actual |
| `--local` | Solo el repositorio actual (sobrescribe al global) |
| `--system` | Todos los usuarios del sistema operativo |

---

## Martes 21/04 — Estados, Commits y .gitignore

### Los 3 estados de Git

Git maneja tus archivos en tres zonas distintas. Entender esto es fundamental.

```
┌─────────────────────┐        ┌─────────────────────┐        ┌─────────────────────┐
│   Working Directory │        │    Stage (Index)     │        │     Repository      │
│                     │──────▶ │                      │──────▶ │                     │
│  Archivos editados  │git add │  Cambios preparados  │ commit │  Historial de commits│
│  (sin confirmar)    │        │  para confirmar      │        │  (.git/)            │
└─────────────────────┘        └─────────────────────┘        └─────────────────────┘
```

```bash
# Ver estado actual de tus archivos
git status

# Agregar TODOS los archivos modificados al stage
git add .

# Agregar un archivo específico al stage
git add src/miarchivo.js

# Sacar TODOS los archivos del stage (vuelven a Working)
git restore --staged .

# Sacar un archivo específico del stage
git restore --staged src/miarchivo.js

# Descartar cambios en Working Directory (irreversible)
git restore src/miarchivo.js
```

---

### Commits — Confirmando cambios

Un commit es una *fotografía* del estado de tu proyecto en un momento dado. Se recomienda usar **prefijos convencionales** en el mensaje.

```bash
# Commit rápido con mensaje corto
git commit -m "feat: agregar login de usuario"

# Commit con cuerpo extenso (abre Nano o VIM)
git commit

# Modificar el ÚLTIMO commit (solo antes de hacer push)
git commit --amend -m "fix: corregir validación de email"

# Ver historial visual de commits
git log --oneline --graph --all
```

#### Prefijos convencionales (Conventional Commits)

| Prefijo | Cuándo usarlo |
|---------|---------------|
| `feat:` | Nueva funcionalidad |
| `fix:` | Corrección de un bug |
| `docs:` | Cambios en documentación |
| `style:` | Formato, espacios, comas (sin cambio de lógica) |
| `refactor:` | Refactorización de código existente |
| `test:` | Agregar o modificar tests |
| `chore:` | Tareas de mantenimiento, dependencias |

> Para más detalle buscar en internet: **Conventional Commits** o **Git Flow prefixes**.

#### Commits con cuerpo (descripción larga)

Cuando el mensaje corto no es suficiente, se abre el editor con `git commit` y se escribe así:

```
feat: implementar autenticación con JWT
                                         ← línea en blanco OBLIGATORIA
Se agrega sistema de login usando JSON Web Tokens.
- Validación de usuario y contraseña en /api/auth/login
- Token expira en 24 horas
- Se agrega middleware de protección de rutas
```

La primera línea es el **título**, luego una línea en blanco, y a partir de la tercera línea va el **cuerpo** con la explicación detallada.

---

### El archivo `.gitignore`

En `.gitignore` declaras los archivos que Git debe ignorar completamente: dependencias, variables de entorno, archivos compilados, etc.

```gitignore
# Carpeta de dependencias Node.js
node_modules/

# Variables de entorno (NUNCA subir al repo)
.env
.env.local

# Archivos compilados
dist/
build/
*.class
*.pyc

# Archivos del sistema operativo
.DS_Store
Thumbs.db

# Logs
*.log
logs/
```

> **Importante:** Si ya hiciste commit de un archivo que debería ignorarse, necesitas ejecutar `git rm --cached <archivo>` para dejar de rastrearlo sin borrarlo de tu disco.

---

## Miércoles 22/04 — GitHub y conexión SSH

### Por qué SSH y no HTTPS

SSH (Secure Shell) crea un canal cifrado entre tu computadora y GitHub usando un par de llaves criptográficas. Una vez configurado **no necesitas escribir usuario/contraseña** en cada push o pull.

#### Pasos para configurar SSH

```bash
# 1. Generar tu llave SSH con el algoritmo ED25519 (moderno y seguro)
ssh-keygen -t ed25519 -C "tu-correo@email.com"

# 2. Mostrar tu llave pública para copiarla
cat ~/.ssh/id_ed25519.pub
```

3. En GitHub: **Perfil → Settings → SSH and GPG keys → New SSH Key**, pegar el contenido, darle un nombre y guardar.

```bash
# 4. Verificar que la conexión funciona
ssh -T git@github.com
# Respuesta esperada: Hi TuUser! You've successfully authenticated...
```

---

### Conectar repositorio local con GitHub (primer push)

```bash
# Vincular tu repo local con el remoto
git remote add origin git@github.com:TuUser/TuRepo.git

# Renombrar la rama principal a "main"
git branch -M main

# Subir por primera vez y vincular la rama
git push -u origin main

# Verificar los remotos configurados
git remote -v
```

---

## Jueves 23/04 — Git Remote, múltiples cuentas SSH y Checkout

### Git Remote — Gestionar repositorios remotos

```bash
# Ver las URLs configuradas
git remote -v

# Conectar repo local con uno remoto
git remote add <apodo> "url"

# Cambiar la URL de un remoto existente
git remote set-url <apodo> "url"

# Eliminar un remoto
git remote remove <apodo>
```

---

### Múltiples cuentas GitHub con SSH

Si tienes cuenta personal y de trabajo, necesitas **una llave SSH por cuenta** y configurar un alias en `~/.ssh/config`.

```bash
# Crear llave para la segunda cuenta
ssh-keygen -t ed25519 -C "trabajo@empresa.com" -f ~/.ssh/id_trabajo
```

Configurar el archivo `~/.ssh/config`:

```
# Cuenta principal
Host github.com
  HostName     github.com
  User         git
  IdentityFile ~/.ssh/id_ed25519

# Segunda cuenta (alias personalizado)
Host github-trabajo
  HostName     github.com
  User         git
  IdentityFile ~/.ssh/id_trabajo
```

```bash
# Verificar la segunda cuenta
ssh -T git@github-trabajo

# Clonar usando el alias configurado
git clone git@github-trabajo:empresa/repositorio.git

# Config local del repo para la segunda cuenta (no global)
git config user.name  "Nombre Trabajo"
git config user.email "yo@empresa.com"
```

---

### Git Checkout — Navegar el historial

| Comando | Descripción |
|---------|-------------|
| `git checkout <rama>` | Cambiar a una rama existente |
| `git checkout <hash>` | Ir a un commit específico del pasado |
| `git checkout -b <rama>` | Crear nueva rama y cambiarse a ella |
| `git checkout main` | Volver a la rama principal |

```bash
# Ir a un commit antiguo
git checkout abc1234

# Volver a la rama actual
git checkout main
```

---

### Estado Detached HEAD

Cuando haces `git checkout <hash>` quedas en un estado especial: **Detached HEAD**. No estás en ninguna rama, estás directamente apuntando a un commit.

> ⚠️ Si haces commits en Detached HEAD, esos cambios pueden perderse cuando vuelvas a una rama normal.

Solución: crear una rama nueva antes de trabajar:

```bash
git checkout -b nueva-rama-experimental
```

**Buenas prácticas:**

- Hacer commit antes de moverte entre ramas
- No trabajar mucho tiempo en estado Detached HEAD
- Crear ramas nuevas para experimentar
- Usar `git switch` en lugar de `git checkout` para mayor claridad

---

## Lunes 27/04 — Ramas y Gitflow

### Comandos esenciales de ramas

```bash
# Listar ramas locales
git branch

# Listar ramas locales Y remotas
git branch -a

# Crear nueva rama (sin cambiarse)
git branch feature/login

# Cambiarse a una rama (forma clásica)
git checkout feature/login

# Crear y cambiarse en un solo comando
git checkout -b feature/login

# Forma moderna y más segura (solo para ramas)
git switch feature/login
git switch -c feature/login   # crear + cambiarse

# Eliminar rama ya mergeada
git branch -d feature/login

# Forzar eliminación
git branch -D feature/login

# Renombrar la rama actual
git branch -m nuevo-nombre
```

> **`git checkout` vs `git switch`:** `git switch` fue introducido en Git 2.23 para trabajar exclusivamente con ramas. Es más seguro porque no permite ir a commits sueltos accidentalmente.

---

### Gitflow — Flujo de trabajo profesional por ramas

Gitflow es una convención que organiza el trabajo en equipo usando ramas con nombres y propósitos bien definidos.

```
         ┌─────── hotfix/xxx ────────────────────────────┐
         │                                               │
─────────●──────────────────────────────────────────────●──── main
         │                                               │
         └──●────────────────────────────────────────●───── release/x.x
            │                                        │
────────────●────────●──────────────────────●────────●──── develop
                     │                      │
                     └──── feature/xxx ─────┘
```

> Referencia visual completa: [Diagrama Gitflow original — nvie.com](https://nvie.com/img/git-model@2x.png)

#### Las 5 ramas de Gitflow

| Rama | Origen | Destino | Propósito |
|------|--------|---------|-----------|
| `main` | — | — | Código en **producción**. Solo recibe merges de `release/*` y `hotfix/*` |
| `develop` | `main` | — | Rama de **integración**. Base del próximo release |
| `feature/*` | `develop` | `develop` | Cada nueva funcionalidad. Ej: `feature/login` |
| `release/*` | `develop` | `main` + `develop` | Preparar nueva versión: ajustes finales, changelog |
| `hotfix/*` | `main` | `main` + `develop` | Bugs críticos en producción que no pueden esperar |

---

## Martes 28/04 — Merge, Fetch, Pull, Push y flujo sin PR

### Git Merge — Fusionar ramas

```bash
# Merge estándar (fast-forward si es posible)
git merge feature/login

# Merge forzando commit de merge (--no-ff)
# Conserva el historial visual de ramas
git merge --no-ff feature/login

# Configurar --no-ff como comportamiento global (recomendado)
git config --global merge.ff false

# Abortar un merge con conflictos
git merge --abort
```

> **Fast-forward vs --no-ff:** El fast-forward mueve el puntero de rama sin crear commit extra (historial lineal). El `--no-ff` siempre crea un commit de merge, preservando la historia visual de qué cambios vinieron de qué rama.

#### Resolver conflictos de merge

Los conflictos ocurren cuando dos ramas modificaron las mismas líneas de un archivo. Git marca las diferencias así:

```
<<<<<<< HEAD
código de tu rama actual
=======
código de la rama que estás mergeando
>>>>>>> feature/login
```

Se edita manualmente el archivo, se deja solo lo correcto y luego:

```bash
git add .
git commit
```

---

### Fetch, Pull y Push

| Comando | Descripción |
|---------|-------------|
| `git fetch` | Descarga cambios del remoto pero **no los aplica**. Los deja en `origin/<rama>` para revisión |
| `git pull origin <rama>` | Hace fetch + merge automáticamente |
| `git push origin <rama>` | Sube commits locales al repositorio remoto |
| `git push -u origin <rama>` | Primera subida: sube y vincula la rama local con la remota |

```bash
git fetch
git pull origin develop
git push origin feature/login
git push -u origin feature/nueva    # primera vez
```

> **Nota:** Preferir `git fetch` + revisar + `git merge` sobre `git pull` directo te da más control sobre los cambios antes de integrarlos.

---

### Flujo de trabajo sin Pull Requests (colaborador directo)

```bash
# 1. Actualizar develop local
git checkout develop
git fetch
git pull origin develop

# 2. Crear o cambiar a tu rama
git checkout -b feature/mi-funcionalidad

# 3. Trabajar y hacer commits
git add .
git commit -m "feat: ..."

# 4. Subir tu rama al remoto
git push origin feature/mi-funcionalidad

# 5. Traer últimos cambios de develop a tu rama
git merge develop

# 6. Ir a develop y mergear tu rama
git checkout develop
git fetch
git pull origin develop
git merge --no-ff feature/mi-funcionalidad

# 7. Resolver conflictos si los hay, luego subir
git push origin develop

# 8. Eliminar la rama terminada
git branch -D feature/mi-funcionalidad
```

---

## Miércoles 29/04 — Pull Requests y protección de ramas

### ¿Qué es un Pull Request?

Un **Pull Request (PR)** es una solicitud formal para fusionar los cambios de una rama en otra. Antes del merge, el equipo puede revisar el código, dejar comentarios y aprobar o rechazar los cambios. Es la forma profesional de trabajar en equipo.

> **Pull Request** en GitHub = **Merge Request** en GitLab. Mismo concepto, diferente nombre.

**Por qué usar PRs:**

- Revisar código línea por línea antes de integrar
- Generar discusión: comentarios, sugerencias, preguntas
- Evitar errores: otro dev aprueba o rechaza los cambios
- Registro histórico: queda documentado quién, qué y por qué
- Bloquear merges directos a `main` o `develop`

---

### Flujo de trabajo con Pull Requests

```bash
# 1. Actualizar develop y crear tu rama
git checkout develop
git pull origin develop
git checkout -b feature/xxx

# 2. Desarrollar y commitear normalmente
git add .
git commit -m "feat: ..."

# 3. Actualizar tu rama con cambios recientes de develop
git merge develop

# 4. Resolver conflictos si los hay y subir
git add .
git commit
git push origin feature/xxx
```

5. En GitHub: **"Compare & Pull Request"** → base: `develop`, compare: `feature/xxx` → Agregar título y descripción → **Create Pull Request**
6. Esperar revisión y aprobación del equipo
7. Una vez aprobado → Merge en GitHub → Eliminar la rama remota

```bash
# 8. Limpiar localmente
git checkout develop
git pull origin develop
git branch -D feature/xxx
```

---

### Rulesets y protección de ramas en GitHub

Se configura en **Settings → Branches → Rulesets** (o Branch Protection Rules).

| Configuración | Descripción |
|---------------|-------------|
| `Require a pull request` | Nadie puede hacer push directo a la rama, obliga a usar PR |
| `Required approvals: 1+` | El PR necesita X aprobaciones antes de mergearse |
| `Dismiss stale reviews` | Si hay nuevos commits, las aprobaciones anteriores se invalidan |
| `Require status checks` | Los tests de CI deben pasar antes de permitir el merge |
| `Block force pushes` | Previene reescribir el historial en esa rama |
| `Restrict who can merge` | Solo ciertos roles pueden aprobar merges a `main` |

---

## Jueves 30/04 — Git Stash y Git Diff

### Git Stash — Guardar cambios temporalmente

El stash es una *pila temporal* donde puedes guardar trabajo sin terminar para limpiar tu Working Directory sin necesidad de hacer commit. Útil cuando necesitas cambiar de rama de urgencia.

```bash
# Guardar cambios en el stash
git stash

# Guardar con descripción (recomendado)
git stash push -m "WIP: login form en progreso"

# Incluir archivos nuevos (untracked) en el stash
git stash push -u -m "con archivos nuevos"

# Ver lista de stashes guardados
git stash list
# Salida: stash@{0}: ..., stash@{1}: ...

# Aplicar el último stash Y eliminarlo de la lista
git stash pop

# Aplicar un stash específico sin eliminarlo
git stash apply stash@{2}

# Ver qué contiene un stash sin aplicarlo
git stash show -p stash@{0}

# Eliminar un stash específico
git stash drop stash@{1}

# Limpiar TODOS los stashes
git stash clear
```

> El stash funciona como una **pila (stack):** último en entrar, primero en salir (LIFO — Last In, First Out).

---

### Git Diff — Comparar diferencias

`git diff` muestra exactamente qué líneas cambiaron: las precedidas por `-` fueron eliminadas y las con `+` fueron agregadas.

```bash
# Ver cambios en Working Directory (no staged)
git diff

# Ver cambios de un archivo específico (no staged)
git diff src/index.js

# Ver cambios que YA están en stage
git diff --staged
git diff --cached   # equivalente

# Ver staged de un archivo específico
git diff --staged src/index.js

# Comparar diferencias entre dos ramas
git diff develop feature/login

# Comparar dos commits
git diff abc1234 def5678

# Solo mostrar nombres de archivos que cambiaron
git diff --name-only develop feature/login

# Resumen estadístico de cambios
git diff --stat
```

| Zona a comparar | Comando |
|-----------------|---------|
| Working → último commit | `git diff` |
| Stage → último commit | `git diff --staged` |
| Rama A vs Rama B | `git diff ramaA ramaB` |
| Commit A vs Commit B | `git diff hash1 hash2` |

---

## Cheatsheet General

| Comando | Descripción |
|---------|-------------|
| `git init` | Inicializar repositorio local |
| `git clone <url>` | Clonar repositorio remoto |
| `git status` | Ver estado de archivos |
| `git add .` | Agregar todo al stage |
| `git restore --staged .` | Sacar todo del stage |
| `git commit -m "..."` | Confirmar cambios con mensaje corto |
| `git commit --amend` | Modificar el último commit |
| `git log --oneline --graph --all` | Ver historial visual |
| `git branch` | Listar ramas |
| `git switch -c <rama>` | Crear y cambiarse a nueva rama |
| `git merge --no-ff <rama>` | Fusionar ramas conservando historial |
| `git merge --abort` | Cancelar merge en conflicto |
| `git fetch` | Bajar cambios sin aplicar |
| `git pull origin <rama>` | Bajar y aplicar cambios |
| `git push origin <rama>` | Subir cambios al remoto |
| `git push -u origin <rama>` | Subir y vincular rama nueva |
| `git remote -v` | Ver remotos configurados |
| `git stash push -m "..."` | Guardar trabajo en progreso |
| `git stash pop` | Recuperar último stash |
| `git diff --staged` | Ver cambios listos para commit |
| `git diff <ramaA> <ramaB>` | Comparar dos ramas |

---

<div align="center">

**Apuntes Git & GitHub** · Clase con Auxigood Isac · Abril–Mayo 2026

![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)

</div>