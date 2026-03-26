# Guía completa de Git — desde cero hasta avanzado

Bienvenido: esta guía explica Git paso a paso, desde la configuración inicial hasta comandos avanzados, con ejemplos claros y agrupada por niveles (Básico / Intermedio / Avanzado / Especiales). Está escrita para alguien que empieza y quiere profundizar progresivamente — explicada con lenguaje simple y ejemplos prácticos.

---

**Índice**

- Introducción rápida
- Instalación
- Configuración inicial
- Nivel Básico: primeros pasos (crear, clonar, añadir, confirmar)
- Nivel Intermedio: ramas, fusión, colaboración y remotos
- Nivel Avanzado: rebase, reflog, bisect, cherry-pick, reset, revert
- Trabajo con múltiples repos y módulos: `submodule`, `subtree`, orquestador
- Herramientas y flujos especiales: `worktree`, `sparse-checkout`, `LFS`
- Git plumbing (comandos por debajo de los comandos comunes)
- Recuperación y mantenimiento: `gc`, `fsck`, `filter-repo` / `filter-branch`
- Hooks y automatización
- Buenas prácticas y consejos
- Hoja de referencia (cheat sheet)
- Recursos y lecturas recomendadas

---

**Introducción rápida**

Git es un sistema de control de versiones. Piensa en Git como un cuaderno donde guardas cambios de tus archivos, y puedes viajar en el tiempo, colaborar con otros y probar ideas sin romper lo que ya funciona.

Conceptos clave — explicado para un niño:

- Repositorio: la caja donde guardas tu proyecto.
- Commit: una foto de cómo está tu proyecto ahora.
- Branch/ramas: caminos alternativos para trabajar sin estropear la versión principal.
- Remote/remoto: la copia de esa caja que guardas en Internet (por ejemplo en GitHub).

---

**Instalación**

- Linux (Debian/Ubuntu):

```bash
sudo apt update
sudo apt install git
```

- macOS (Homebrew):

```bash
brew install git
```

- Windows: instalar desde https://git-scm.com o usar Git para Windows (incluye Git Bash).

Ver la versión:

```bash
git --version
```

---

**Configuración inicial (primeros pasos)**

Configura tu identidad (es importante para los commits):

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@ejemplo.com"
```

Ver configuración:

```bash
git config --list
```

Configurar editor por defecto:

```bash
git config --global core.editor "nano"
```

Configurar alias útiles (opcionales):

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
```

Configurar credenciales (ejemplo con cache en Linux):

```bash
git config --global credential.helper cache   # guarda credenciales en memoria por un tiempo
```

---

**Nivel Básico — conceptos y comandos esenciales**

1. Crear un repositorio local nuevo:

```bash
mkdir mi-proyecto
cd mi-proyecto
git init
```

Explicación: `git init` crea la estructura para que Git empiece a llevar el control.

2. Clonar un repositorio remoto:

```bash
git clone https://github.com/usuario/repo.git
```

Explicación: baja una copia completa del proyecto.

3. Ver el estado:

```bash
git status
```

4. Añadir archivos al área de preparación (staging):

```bash
git add archivo.txt
git add .     # añade todos los cambios nuevos y modificados
```

5. Hacer un commit (guardar una foto):

```bash
git commit -m "Mensaje claro describiendo el cambio"
```

6. Ver historial de commits:

```bash
git log
git log --oneline --graph --all
```

7. Visualizar diferencias:

```bash
git diff          # cambios no añadidos aún
git diff --staged # cambios añadidos al staging
```

8. Ignorar archivos (archivo `.gitignore`):

Ejemplo `.gitignore`:

```
# Ignorar archivos compilados
*.o
# Ignorar carpetas temporales
.cache/
# Ignorar credenciales locales
.env
```

9. Deshacer cambios simples:

```bash
git checkout -- archivo.txt    # descarta cambios en un archivo (antes de añadir)
git reset HEAD archivo.txt     # saca del staging (si lo añadiste)
```

10. Eliminar archivos:

```bash
git rm archivo.txt
git commit -m "Eliminar archivo"
```

---

**Nivel Intermedio — ramas, fusión, colaboración y remotos**

Ramas básicas:

```bash
git branch               # lista ramas
git branch nueva-rama    # crear rama
git checkout nueva-rama  # cambiar a rama
git switch nueva-rama    # alternativa moderna a checkout
git checkout -b rama     # crear y cambiar a la rama
```

Trabajar con ramas — flujo típico:

```bash
git checkout -b feature-x
# trabajar
git add .
git commit -m "Agregar feature x"
git checkout main
git merge feature-x
```

Fusiones (merge) y conflictos:

- `git merge rama` integra cambios; si hay conflictos, Git te avisará y deberás editarlos, luego `git add` y `git commit`.
- Resolver conflictos: busca los marcadores `<<<<<<<`, `=======`, `>>>>>>>` en los archivos, elige la versión correcta y confirma.

Rebase (alternativa para mantener historial lineal):

```bash
git checkout feature
git rebase main
```

Explicación: `rebase` pone tus commits encima de la punta actual de `main`. Usar con cuidado en ramas públicas.

Remotos (trabajar con repositorios en línea):

```bash
git remote add origin git@github.com:usuario/repo.git
git remote -v
git fetch origin
git pull origin main    # trae y fusiona
git push origin main    # sube commits
```

Explicación de `fetch` vs `pull`:

- `git fetch` descarga cambios del remoto pero no los mezcla automáticamente.
- `git pull` hace `fetch` + `merge` (o `rebase` si lo configuras así).

Trabajar en equipo — flujo recomendado (feature branches):

1. `git checkout -b feature-x`
2. Hacer commits locales claros
3. `git push origin feature-x`
4. Abrir Pull Request en la plataforma (GitHub/GitLab)
5. Revisar, corregir, mergear

Etiquetas (tags):

```bash
git tag v1.0.0
git tag -a v1.0.0 -m "Versión 1.0.0"
git push origin --tags
```

Reescribir último commit (ej. agregar cambios o corregir mensaje):

```bash
git commit --amend -m "Nuevo mensaje"
```

---

**Nivel Avanzado — comandos potentes y recuperación**

`git reset` — mover HEAD y opcionalmente cambios:

```bash
git reset --soft <commit>   # mueve HEAD, conserva staging y working tree
git reset --mixed <commit>  # (default) mueve HEAD y limpia staging
git reset --hard <commit>   # mueve HEAD y descarta cambios; ¡peligroso!
```

`git revert` — deshacer aplicando commit inverso (seguro para ramas públicas):

```bash
git revert <commit>
```

`git cherry-pick` — aplicar un commit específico a la rama actual:

```bash
git cherry-pick <commit>
```

`git reflog` — registro local de movimientos de `HEAD`; muy útil para recuperar commits perdidos:

```bash
git reflog
git checkout <hash_from_reflog>
```

`git bisect` — búsqueda binaria para encontrar commit que introdujo un bug:

```bash
git bisect start
git bisect bad                # estás en una versión con bug
git bisect good v1.2.3        # o un commit conocido bueno
# git hará saltos, pruebas y marcas good/bad hasta encontrar el culpable
git bisect reset
```

Reescribir historial (peligroso en remoto):

```bash
git rebase -i HEAD~5  # reescribir últimos 5 commits interactivos
```

Historial sucio → `filter-branch` (antiguo) o `git filter-repo` (recomendado) para eliminar datos sensibles — usar con precaución.

Recuperación de archivos borrados accidentalmente:

```bash
git checkout <commit_antiguo> -- ruta/al/archivo
```

---

**Trabajo con múltiples repos / módulos (Submodules, Subtrees, Orquestador)**

Submódulos (`git submodule`) — cuando tienes un repo que usa otros repos exactos (por ejemplo librerías con control independiente):

Agregar submódulo:

```bash
git submodule add https://github.com/usuario/lib.git libs/lib
git commit -m "Añadir submódulo lib"
```

Inicializar submódulos al clonar:

```bash
git clone --recurse-submodules https://.../mi-repo.git
# o después:
git submodule init
git submodule update
```

Actualizar submódulo a la última versión remota (dentro de la carpeta del submódulo):

```bash
cd libs/lib
git fetch
git checkout main
git pull
cd ../..
git add libs/lib
git commit -m "Actualizar submódulo lib"
```

Notas sobre submódulos:

- El submódulo guarda un _commit específico_ del repo externo. Debes actualizar y confirmar ese cambio en el repo principal.
- Pueden ser confusos al principio; requieren disciplina.

Subtrees (`git subtree`) — alternativa para incorporar otro repo dentro del tuyo sin submódulos:

Agregar un subtree:

```bash
git subtree add --prefix=libs/lib https://github.com/usuario/lib.git main --squash
```

Actualizar subtree:

```bash
git subtree pull --prefix=libs/lib https://github.com/usuario/lib.git main --squash
```

Ventaja: más simple para usuarios finales; desventaja: puede complicar la sincronización bidireccional.

Orquestador de repos (mono-repo vs multi-repo):

- Un repo "orquestador" puede coordinar varios repos (scripts, CI) y usar submodules/subtrees o herramientas externas (como `repo`, `lerna` en JS, o `nx`). La elección depende del equipo.

---

**Herramientas y flujos especiales**

Worktrees — múltiples lugares de trabajo para la misma base de Git (útil para revisar PRs o trabajar en varias ramas simultáneamente):

```bash
git worktree add ../mi-proyecto-feature feature-x
# esto crea otra carpeta con la rama feature-x lista para trabajar
```

Sparse-checkout — trabajar con una parte del repositorio (útil en mono-repos grandes):

```bash
git sparse-checkout init --cone
git sparse-checkout set carpeta/que/quiero
```

Git LFS (Large File Storage) — manejar archivos grandes (imágenes, binarios):

```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
git add archivo.psd
git commit -m "Add large file"
```

---

**Git plumbing — comandos de bajo nivel (útiles para scripts y entender internals)**

- `git cat-file -p <hash>`: muestra contenido de un objeto
- `git rev-parse HEAD`: convierte nombres a hashes
- `git update-ref`: actualizar refs manualmente
- `git hash-object -w archivo`: crear un objeto blob

Los comandos de plumbing son poderosos pero avanzados; normalmente no se necesitan para el día a día.

---

**Recuperación y mantenimiento**

Limpieza y optimización:

```bash
git gc --aggressive --prune=now
git fsck --full
```

Eliminar datos sensibles del historial (advertencia: reescribe historial):

1. `git filter-branch` (antiguo) — ejemplo:

```bash
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch ruta/secreta" --prune-empty --tag-name-filter cat -- --all
```

2. `git filter-repo` (recomendado; instalar por separado):

```bash
# instalar git-filter-repo (puede que sea un paquete aparte o pip)
git filter-repo --path ruta/secreta --invert-paths
```

Después de reescribir historial, forzar push al remoto (cuidado):

```bash
git push --force origin main
```

---

**Hooks y automatización**

Los hooks son scripts que Git ejecuta en eventos (pre-commit, pre-push, post-merge, etc.). Están en la carpeta `.git/hooks/`.

Ejemplo: `pre-commit` para correr tests antes de confirmar:

```bash
#!/bin/sh
npm test || { echo "Tests fallaron"; exit 1; }
```

Instala hooks en cada desarrollador o usa herramientas centralizadas como `husky` (JS) o CI en el servidor remoto.

---

**Buenas prácticas**

- Mensajes de commit claros: describe el _por qué_, no solo el _qué_.
- Commits pequeños y atómicos: cada commit debe representar un cambio coherente.
- Mantén `main`/`master` siempre estable.
- Revisa antes de mergear (code review, CI).
- Evita `git push --force` en ramas compartidas sin coordinar con el equipo.
- Escribe y mantiene `.gitignore` apropiado.

---

**Hoja de referencia (cheat sheet) — comandos útiles**

- Configuración:

```bash
git config --global user.name "Nombre"
git config --global user.email "email"
```

- Flujo rápido:

```bash
git clone <repo>
git checkout -b mi-rama
git add .
git commit -m "Mensaje"
git push origin mi-rama
```

- Ver historial:

```bash
git log --oneline --graph --decorate --all
```

- Recuperar archivo de commit anterior:

```bash
git checkout <commit> -- ruta/archivo
```

- Reescribir último commit:

```bash
git commit --amend
```

- Búsqueda de bug con bisect:

```bash
git bisect start
git bisect bad
git bisect good <commit>
```

---

**Recursos y lecturas recomendadas**

- Pro Git (libro gratuito): https://git-scm.com/book/es/v2
- Documentación oficial: https://git-scm.com/docs
- Cursos y guías en plataformas como GitHub Learning Lab

---

Si quieres, puedo:

- dividir esta guía en capítulos separados (por ejemplo `01-basico.md`, `02-intermedio.md`) para facilitar lectura;
- añadir ejemplos prácticos paso a paso con un repositorio de ejemplo y ejercicios;
- generar una versión resumida para impresión (PDF/HTML).

He terminado la actualización del archivo con la guía completa. ¿Quieres que lo separe en varios archivos o que incluya ejercicios prácticos con comandos para ejecutar?
