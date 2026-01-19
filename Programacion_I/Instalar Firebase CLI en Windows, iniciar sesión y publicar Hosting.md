# Contenido de la lección 1
---

## 1) Instalar **NVM** y **Node.js** (Windows) y preparar el “entorno”

```powershell
# 👉 NVM es un administrador de versiones de Node.js.
#    Sirve para tener varias versiones de Node y cambiarlas según el proyecto.
#    En Windows se llama "nvm-windows".
# 1) Descarga NVM para Windows desde: https://github.com/coreybutler/nvm-windows/releases
# 2) Instálalo con el instalador (.exe) aceptando las opciones por defecto.
# 3) Cierra y vuelve a abrir tu terminal (PowerShell) para que se apliquen los cambios.

nvm version
# ^ Comprueba que NVM quedó instalado. Si ves un número de versión, todo bien.

nvm install 20
# ^ Instala Node.js versión 20 (LTS = estable). Elegimos una versión estable
#   para evitar errores de compatibilidad.

nvm use 20
# ^ Activa esa versión para esta terminal. "use" = usar esa versión en este entorno.

node -v
npm -v
# ^ Verifica que Node (motor de JS) y npm (instalador de paquetes) están listos.

# "Entorno virtual" en Node se logra usando NVM (versiones aisladas) + carpeta del proyecto.
# No se crea un "venv" como en Python; aquí el aislamiento práctico es por versión y por proyecto.
```

---

## 2) Crear proyecto con **Create React App**

```powershell
# 👉 Create React App (CRA) crea la estructura inicial de una app React automáticamente.
#    Usamos "npx" para ejecutar el generador sin instalarlo permanentemente.

npx create-react-app rodriguez-juan-leccion1
# ^ Crea una carpeta con ese nombre y dentro pone todo lo necesario para empezar.

cd rodriguez-juan-leccion1
# ^ Entramos a la carpeta del proyecto.

npm start
# ^ Inicia el servidor de desarrollo. Se abrirá el navegador con la app.
#   Si algo ocupa el puerto, acepta cambiar a otro.
```

---

## 3) “Hola Mundo” en React

```powershell
# 👉 Vamos a editar el archivo principal de la interfaz.
#    Puedes abrir el proyecto en tu editor (por ejemplo, VS Code):
code .
# ^ Abre la carpeta actual en VS Code (si tienes instalado el comando "code").
```

```jsx
// Ruta del archivo: src/App.js
// 👉 Este componente es lo que se ve en pantalla. Cambiamos su contenido a “Hola Mundo”.

import React from "react"; // <- Importa React para poder usar JSX (la sintaxis parecida a HTML)
import "./App.css";       // <- Importa estilos (opcional)

function App() {
  // {/* Un "componente" es una función que devuelve JSX (lo que se dibuja en la pantalla) */}
  return (
    <div className="App">
      {/* Texto que el usuario verá. Es nuestro "Hola Mundo". */}
      <h1>Hola Mundo</h1>

      {/* Explicación breve:
          - <h1>...</h1> es un encabezado grande.
          - JSX permite escribir etiquetas como HTML dentro de JavaScript.
          - Todo debe ir dentro de un único elemento padre (aquí, <div>). */}
    </div>
  );
}

export default App; // <- Exportamos el componente para que la app lo use
```

```powershell
# Guarda los cambios. La página del navegador se recargará sola y mostrará "Hola Mundo".
```

---

## 4) Instalar **Firebase CLI** en Windows, iniciar sesión y publicar (Hosting)

```powershell
# 👉 Firebase CLI es una herramienta de línea de comandos para trabajar con Firebase (servicios de Google).
#    "Hosting" permite subir la app para que esté disponible en internet.

npm install -g firebase-tools
# ^ Instala la herramienta globalmente para poder usar el comando "firebase" en cualquier proyecto.

firebase --version
# ^ Comprueba que quedó instalada.

firebase login
# ^ Abre el navegador para iniciar sesión con tu cuenta de Google.
#   Esto da permiso a la CLI para actuar en tu cuenta de Firebase (seguro y necesario).
```

```powershell
# 👉 Preparamos la app React para producción (archivos optimizados y listos para subir).

npm run build
# ^ Genera una carpeta "build" con la versión lista para publicar.
```

```powershell
# 👉 Inicializamos la configuración de Firebase Hosting en ESTE proyecto.
#    Necesitas tener un proyecto creado en la consola de Firebase (web) previamente.

firebase init hosting
# ^ Pasos interactivos (elige con flechas y Enter):
#   - "Use an existing project"  -> selecciona tu proyecto de Firebase.
#   - "What do you want to use as your public directory?" -> escribe: build
#     (porque CRA genera los archivos finales en "build")
#   - "Configure as a single-page app (rewrite all urls to /index.html)?" -> Yes
#     (así React maneja las rutas internas sin errores 404)
#   - "Set up automatic builds and deploys with GitHub?" -> No (para hacerlo simple ahora)
#   - Si pregunta "Overwrite ...?" -> Di No, salvo que sepas lo que haces.
```

```powershell
# 👉 Publicamos la app.
firebase deploy
# ^ Sube la carpeta "build" al Hosting de Firebase.
#   Al final te mostrará una URL pública para visitar tu sitio.
```

---

## 5) Comandos útiles de repaso

```powershell
# NVM / Node
nvm list       # Muestra versiones instaladas de Node
nvm install 20 # Instala Node 20 (LTS)
nvm use 20     # Usa Node 20 en esta terminal

# CRA / React
npx create-react-app rodriguez-juan-leccion1
cd rodriguez-juan-leccion1
npm start      # Arranca el servidor local de desarrollo
npm run build  # Empaqueta para producción (carpeta "build")

# Firebase
npm install -g firebase-tools
firebase login
firebase init hosting  # Configura el hosting en este proyecto (elige "build")
firebase deploy        # Publica la web y te da una URL
```

---

## 6) Estructura mínima creada por CRA (para ubicarte)

```text
# 👉 Solo para que identifiques archivos importantes (no tienes que memorizar todo).
rodriguez-juan-leccion1/
├─ node_modules/      # Paquetes que usa el proyecto (los instala npm)
├─ public/            # Archivos estáticos (favicon, index.html base, etc.)
├─ src/               # Código fuente de React (donde editas componentes)
│  ├─ App.js          # Componente principal que editamos para "Hola Mundo"
│  └─ index.js        # Punto de entrada de la app
├─ package.json       # Lista de dependencias y scripts (como "start" y "build")
└─ README.md          # Instrucciones generadas por CRA
```

---


###################################################################################

````markdown
# Conectar React con Firebase usando archivo `.env`

---

## 1) Crear archivo `.env`

```bash
# 👉 Este archivo se coloca en la raíz del proyecto React (junto a package.json).
#    Sirve para guardar claves secretas sin ponerlas directamente en el código.
#    Los nombres de las variables en React deben empezar con "REACT_APP_".

# Ejemplo de configuración de Firebase (usa tus datos del panel de Firebase):
REACT_APP_FIREBASE_API_KEY=tu_api_key_aqui
REACT_APP_FIREBASE_AUTH_DOMAIN=tu_proyecto.firebaseapp.com
REACT_APP_FIREBASE_PROJECT_ID=tu_proyecto
REACT_APP_FIREBASE_STORAGE_BUCKET=tu_proyecto.appspot.com
REACT_APP_FIREBASE_MESSAGING_SENDER_ID=1234567890
REACT_APP_FIREBASE_APP_ID=1:1234567890:web:abcdef123456
````

---

## 2) Instalar Firebase en el proyecto React

```bash
# 👉 Este comando agrega la librería oficial de Firebase para que React pueda usarla.
npm install firebase
```

---

## 3) Crear archivo de configuración `firebase.js`

```javascript
// 👉 Este archivo estará en: src/firebase.js
//    Aquí conectamos la app React con Firebase usando las variables del archivo .env.

import { initializeApp } from "firebase/app"; 
// ^ Importa la función que arranca Firebase

// Configuración de Firebase usando variables del .env
const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY, 
  // ^ process.env = forma de acceder a las variables de entorno

  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_FIREBASE_APP_ID,
};

// Inicializa la app de Firebase
const app = initializeApp(firebaseConfig);

// Exportamos "app" para poder usar Firebase en otros archivos de React
export default app;
```

---

## 4) Usar Firebase en un componente React

```javascript
// 👉 Ejemplo de uso en src/App.js

import React from "react";
import app from "./firebase"; 
// ^ Importa la configuración de Firebase para poder usarla

function App() {
  return (
    <div className="App">
      <h1>Conexión a Firebase lista ✅</h1>
      {/* Aquí podrías empezar a usar servicios de Firebase (Auth, Firestore, etc.) */}
    </div>
  );
}

export default App;
```

---

## 5) Recordatorio importante

```text
# - El archivo .env NO debe subirse a GitHub (porque contiene datos sensibles).
# - Asegúrate de tenerlo en el archivo .gitignore (CRA ya lo incluye por defecto).
# - Cuando cambies el .env debes reiniciar el servidor con: npm start
# - Nunca pongas las claves directamente en el código fuente.
```

```
```