# 📘 Introducción a la sintaxis básica de ReactJS

---

## 1) Ejecutar el **Componente básico**

* Edita el archivo `src/App.js` así:

```jsx
function App() {
  return <h1>¡Hola Mundo!</h1>;
}

export default App;
```

📌 Guarda el archivo → En el navegador verás **¡Hola Mundo!**.

---

## 2) Ejecutar el ejemplo de **JSX**

* Reemplaza `App.js` con:

```jsx
function App() {
  return (
    <div>
      <h1>Hola React</h1>
      <p>Estoy aprendiendo la sintaxis básica.</p>
    </div>
  );
}

export default App;
```

📌 Guarda → Verás el título y el párrafo en pantalla.

---

## 3) Ejecutar el ejemplo de **Variables en JSX**

```jsx
function App() {
  const nombre = "Juan";
  return <h2>Bienvenido, {nombre}</h2>;
}

export default App;
```

📌 Guarda → Verás: **Bienvenido, Juan**.

---

## 4) Ejecutar el ejemplo de **Atributos**

```jsx
function App() {
  return (
    <div>
      <img src="https://via.placeholder.com/150" alt="Imagen de ejemplo" />
    </div>
  );
}

export default App;
```

📌 Guarda → Verás una imagen cargada.

---

## 5) Ejecutar el ejemplo de **Eventos (clic en un botón)**

```jsx
function App() {
  function mostrarAlerta() {
    alert("¡Hiciste clic en el botón!");
  }

  return <button onClick={mostrarAlerta}>Haz clic aquí</button>;
}

export default App;
```

📌 Guarda → Haz clic en el botón → Aparece un mensaje emergente.

---

## 6) Ejecutar el ejemplo de **useState (contador)**

1. Primero, importa `useState` arriba del archivo:

```jsx
import React, { useState } from "react";

function App() {
  const [contador, setContador] = useState(0);

  return (
    <div>
      <p>Has hecho clic {contador} veces</p>
      <button onClick={() => setContador(contador + 1)}>Aumentar</button>
    </div>
  );
}

export default App;
```

📌 Guarda → Haz clic en el botón → El número aumenta.

---

## 7) Ejecutar el ejemplo de **Listas**

```jsx
function App() {
  const frutas = ["Manzana", "Banana", "Naranja"];

  return (
    <ul>
      {frutas.map((fruta, i) => (
        <li key={i}>{fruta}</li>
      ))}
    </ul>
  );
}

export default App;
```

📌 Guarda → Verás una lista con las frutas.

---

# 🚀 Resumen del flujo de trabajo

1. **Crear proyecto React** con `npx create-react-app nombre-del-proyecto`.
2. **Entrar en el proyecto**: `cd nombre-del-proyecto`.
3. **Iniciar servidor**: `npm start`.
4. **Abrir y editar `src/App.js`** → pega cada ejemplo de sintaxis.
5. **Guardar el archivo** → el navegador se actualiza automáticamente.

---