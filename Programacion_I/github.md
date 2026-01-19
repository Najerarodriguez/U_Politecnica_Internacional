### 1. Verificar Git instalado

**Linux / Windows**

```bash
git --version
```

### 2. Configuración inicial

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
git config --list
```

### 3. Crear repositorio local

```bash
mkdir proyecto
cd proyecto
git init
```

### 4. Estado y seguimiento

```bash
git status
git add archivo.txt
git add .
git commit -m "Primer commit"
```

### 5. Historial y cambios

```bash
git log
git log --oneline
git diff
```

### 6. Conectar repositorio remoto (GitHub)

```bash
git remote add origin https://github.com/usuario/repositorio.git
git remote -v
```

### 7. Subir repositorio local a remoto

```bash
git branch -M main
git push -u origin main
```

### 8. Descargar cambios

```bash
git pull
git fetch
```

### 9. Subir cambios

```bash
git push
```

### 10. Clonar repositorio remoto

```bash
git clone https://github.com/usuario/repositorio.git
```

### 11. Ramas

```bash
git branch
git branch nueva-rama
git checkout nueva-rama
git checkout -b otra-rama
git merge nueva-rama
```

### 12. Deshacer y limpiar

```bash
git restore archivo.txt
git reset --hard
git clean -fd
```

### 13. Etiquetas y versiones

```bash
git tag
git tag v1.0
git push origin v1.0
```

### 14. Ayuda rápida

```bash
git help
git help commit
```

Estos comandos cubren el 90% del trabajo real con Git en Linux y Windows.
