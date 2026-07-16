# SRP y OCP explicados con una lista de tareas en React y Firebase

## Objetivo

Este documento explica, de forma sencilla, dos principios importantes de programación:

* **SRP: Principio de Responsabilidad Única**
* **OCP: Principio Abierto/Cerrado**

El ejemplo usa una aplicación de tareas hecha con **React** y conectada a **Firebase Realtime Database**.

La aplicación tiene dos versiones:

1. Una versión monolítica: todo está mezclado en un solo archivo.
2. Una versión refactorizada: cada archivo tiene una responsabilidad clara.

---

# 1. Conceptos importantes

## ¿Qué es SRP?

**SRP significa Single Responsibility Principle.**

En español significa:

> Cada parte del código debe tener una sola responsabilidad.

Explicación para un niño de 7 años:

Imagina que tienes una caja de juguetes.

Si en la misma caja guardas juguetes, ropa, comida, lápices y zapatos, después será difícil encontrar algo.

Pero si tienes:

* Una caja para juguetes.
* Una caja para lápices.
* Una caja para ropa.
* Una caja para comida.

Todo queda más ordenado.

En programación pasa igual.

Un archivo no debería hacer demasiadas cosas al mismo tiempo.

---

## Ejemplo de SRP en esta aplicación

Una aplicación de tareas puede tener varias responsabilidades:

* Mostrar la pantalla.
* Escribir una nueva tarea.
* Guardar la tarea.
* Leer tareas desde Firebase.
* Validar que el texto no esté vacío.
* Conectar con Firebase.

Con SRP, cada responsabilidad se separa en archivos diferentes.

---

## ¿Qué es OCP?

**OCP significa Open/Closed Principle.**

En español significa:

> El código debe estar abierto para extenderse, pero cerrado para modificarse.

Explicación para un niño de 7 años:

Imagina que tienes un carrito de juguete.

Si quieres ponerle una bandera, una calcomanía o una luz, lo ideal es poder agregar esas cosas sin romper el carrito.

En programación pasa igual.

Un componente debe permitir agregar nuevas funciones sin tener que cambiar su código interno.

---

## Ejemplo de OCP en esta aplicación

El componente `DataList` no sabe si muestra tareas, usuarios, productos o mensajes.

Solo recibe:

* Una lista de elementos.
* Una forma de mostrar cada elemento.
* Una forma de obtener la llave de cada elemento.
* Un texto para mostrar si la lista está vacía.

Eso permite usar el mismo componente para muchas cosas sin modificarlo.

---

# 2. Problema: componente monolítico

El archivo `src/TasksMonolith.js` funciona, pero tiene un problema: hace demasiadas cosas en un solo lugar.

Este archivo mezcla:

* Conexión con Firebase.
* Lectura de datos.
* Escritura de datos.
* Estado de React.
* Validaciones.
* Formulario.
* Lista visual.
* Pantalla principal.

Eso viola SRP porque el archivo tiene muchas responsabilidades.

---

# 3. Explicación de `src/TasksMonolith.js`

## Importaciones

```js
import { useEffect, useState } from "react";
import { initializeApp } from "firebase/app";
import { getDatabase, onValue, push, ref } from "firebase/database";
```

Estas líneas traen herramientas necesarias.

* `useState`: guarda información en memoria.
* `useEffect`: ejecuta acciones cuando el componente carga.
* `initializeApp`: inicia Firebase.
* `getDatabase`: obtiene la base de datos.
* `onValue`: escucha cambios en Firebase.
* `push`: agrega datos nuevos.
* `ref`: indica la ubicación dentro de Firebase.

Problema: el componente ya empieza mezclando React con Firebase.

---

## Configuración de Firebase

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  databaseURL: "...",
  projectId: "...",
};
```

Aquí están los datos para conectar la aplicación con Firebase.

Problema: esta configuración está dentro del mismo archivo que muestra la pantalla.

Eso no es buena práctica porque la conexión con Firebase debería estar separada.

---

## Creación de la base de datos

```js
const db = getDatabase(initializeApp(firebaseConfig));
```

Esta línea inicia Firebase y obtiene la base de datos.

Problema: el componente también se encarga de preparar la conexión a la base de datos.

Eso no debería estar en el mismo archivo que muestra tareas.

---

## Estado del formulario y de las tareas

```js
const [text, setText] = useState("");
const [tasks, setTasks] = useState([]);
```

Aquí se crean dos estados:

* `text`: guarda lo que el usuario escribe.
* `tasks`: guarda la lista de tareas que vienen desde Firebase.

Esta parte sí pertenece más a React, pero en el componente monolítico está mezclada con muchas otras responsabilidades.

---

## Lectura de tareas desde Firebase

```js
useEffect(
  () =>
    onValue(ref(db, "tasks"), (snapshot) => {
      const data = snapshot.val() || {};

      setTasks(
        Object.entries(data).map(([id, task]) => ({
          id,
          ...task,
        }))
      );
    }),
  []
);
```

Esta parte hace lo siguiente:

1. Busca las tareas en Firebase.
2. Escucha cambios en la ruta `"tasks"`.
3. Convierte los datos de Firebase en una lista.
4. Guarda esa lista en `tasks`.

Problema: esta lógica no debería estar directamente en la pantalla.

La pantalla solo debería mostrar datos, no saber todos los detalles de cómo Firebase entrega la información.

---

## Función para agregar tareas

```js
const submit = async (event) => {
  event.preventDefault();

  const value = text.trim();

  if (!value) return;

  await push(ref(db, "tasks"), {
    text: value,
    createdAt: Date.now(),
  });

  setText("");
};
```

Esta función hace varias cosas:

1. Evita que el formulario recargue la página.
2. Limpia espacios del texto.
3. Valida que el texto no esté vacío.
4. Guarda la tarea en Firebase.
5. Limpia el campo de texto.

Problema: mezcla lógica del formulario, validación y acceso a Firebase.

Con SRP, esas responsabilidades deberían separarse.

---

## Interfaz visual

```js
return (
  <main>
    <h1>Tareas</h1>

    <form onSubmit={submit}>
      <input
        value={text}
        onChange={(event) => setText(event.target.value)}
        placeholder="Nueva tarea"
      />

      <button>Agregar</button>
    </form>

    <ul>
      {tasks.map((task) => (
        <li key={task.id}>{task.text}</li>
      ))}
    </ul>
  </main>
);
```

Esta parte muestra:

* El título.
* El formulario.
* El campo de texto.
* El botón.
* La lista de tareas.

Esta responsabilidad sí es de presentación.

El problema es que el mismo archivo también conecta Firebase, lee datos, guarda datos y valida información.

---

# 4. Por qué el componente monolítico es una mala práctica

El archivo `TasksMonolith.js` funciona, pero no está bien organizado.

## Problemas principales

| Problema                           | Explicación                                        |
| ---------------------------------- | -------------------------------------------------- |
| Tiene demasiadas responsabilidades | Hace conexión, lógica, validación y pantalla       |
| Es difícil de modificar            | Cualquier cambio puede romper otra parte           |
| Es difícil de probar               | No se puede probar la lógica sin tocar la pantalla |
| Es difícil de reutilizar           | Todo está amarrado a tareas y Firebase             |
| Viola SRP                          | No tiene una sola responsabilidad                  |
| No aplica bien OCP                 | Para extenderlo normalmente habría que modificarlo |

---

# 5. Refactorización aplicando SRP y OCP

La refactorización consiste en ordenar el código.

La idea es pasar de un solo archivo grande a varios archivos pequeños.

Estructura final:

```txt
src/
├── components/
│   ├── DataList.js
│   └── TaskForm.js
├── hooks/
│   └── useTasks.js
├── pages/
│   └── TasksPage.js
├── services/
│   └── taskService.js
├── firebase.js
└── App.js
```

---

# 6. Responsabilidad de cada archivo

## `src/firebase.js`

Responsabilidad:

> Conectar la aplicación con Firebase.

Este archivo solo inicializa Firebase y exporta la base de datos.

```js
const app = initializeApp(firebaseConfig);

export const db = getDatabase(app);
```

Aplicación de SRP:

* Solo maneja la conexión con Firebase.
* No muestra pantallas.
* No guarda tareas.
* No valida datos.

---

## `src/services/taskService.js`

Responsabilidad:

> Leer y escribir tareas en Firebase.

Este archivo contiene las funciones que hablan directamente con Firebase.

```js
export const subscribeTasks = (onChange) =>
  onValue(tasksRef, (snapshot) => {
    const data = snapshot.val() || {};

    onChange(
      Object.entries(data).map(([id, task]) => ({
        id,
        ...task,
      }))
    );
  });

export const createTask = (task) => push(tasksRef, task);
```

Aplicación de SRP:

* Solo se encarga del acceso a datos.
* No muestra formularios.
* No controla estados visuales.
* No renderiza listas.

---

## `src/hooks/useTasks.js`

Responsabilidad:

> Manejar la lógica de negocio de las tareas.

Este hook decide cómo se obtienen y agregan tareas.

```js
const addTask = async (rawText) => {
  const text = rawText.trim();

  if (!text) return false;

  await createTask({
    text,
    createdAt: Date.now(),
  });

  return true;
};
```

Aquí se valida que la tarea no esté vacía.

También se agrega la fecha de creación.

Aplicación de SRP:

* Se encarga de la lógica de tareas.
* No sabe cómo se ve el formulario.
* No muestra HTML.
* No contiene configuración de Firebase.

---

## `src/components/TaskForm.js`

Responsabilidad:

> Mostrar el formulario para agregar una tarea.

Este componente solo controla el campo de texto y llama a `onAdd`.

```js
export default function TaskForm({ onAdd }) {
  const [text, setText] = useState("");

  const submit = async (event) => {
    event.preventDefault();

    if (await onAdd(text)) {
      setText("");
    }
  };
}
```

Aplicación de SRP:

* Solo muestra y controla el formulario.
* No sabe si los datos se guardan en Firebase, MySQL o una API.
* No decide cómo se almacenan las tareas.

---

## `src/components/DataList.js`

Responsabilidad:

> Mostrar una lista de datos.

Este componente es genérico.

```js
export default function DataList({
  items,
  renderItem,
  getKey = (item) => item.id,
  emptyText = "No hay elementos",
}) {
```

Puede mostrar tareas, productos, usuarios o cualquier otra lista.

Aplicación de OCP:

El componente está abierto para extenderse porque se puede cambiar su comportamiento usando props:

* `items`
* `renderItem`
* `getKey`
* `emptyText`

Pero está cerrado para modificarse porque no hay que cambiar su código interno para usarlo con diferentes datos.

Ejemplo:

```js
<DataList
  items={tasks}
  renderItem={(task) => task.text}
  emptyText="No hay tareas"
/>
```

Si después se quiere mostrar usuarios:

```js
<DataList
  items={users}
  renderItem={(user) => user.name}
  emptyText="No hay usuarios"
/>
```

No se cambia `DataList.js`.

Solo se reutiliza.

---

## `src/pages/TasksPage.js`

Responsabilidad:

> Armar la pantalla de tareas usando piezas pequeñas.

Este archivo une:

* El formulario.
* La lista.
* La lógica de tareas.

```js
const { tasks, addTask } = useTasks();
```

Luego muestra:

```js
<TaskForm onAdd={addTask} />

<DataList
  items={tasks}
  renderItem={(task) => task.text}
  emptyText="No hay tareas"
/>
```

Aplicación de SRP:

* La página organiza componentes.
* No contiene la conexión directa con Firebase.
* No contiene toda la lógica interna del formulario.
* No contiene detalles técnicos de la base de datos.

---

## `src/App.js`

Responsabilidad:

> Mostrar la página principal de la aplicación.

```js
export default function App() {
  return <TasksPage />;
}
```

Es un archivo simple.

Solo carga la pantalla principal.

---

# 7. Cómo se convierte la aplicación monolítica en una aplicación refactorizada

## Paso 1: separar Firebase

Antes, Firebase estaba dentro de `TasksMonolith.js`.

Después, se mueve a:

```txt
src/firebase.js
```

Resultado:

* La conexión queda centralizada.
* Si cambia la configuración, solo se modifica un archivo.

---

## Paso 2: separar el acceso a datos

Antes, el componente usaba directamente:

```js
onValue(ref(db, "tasks"), ...)
push(ref(db, "tasks"), ...)
```

Después, eso se mueve a:

```txt
src/services/taskService.js
```

Resultado:

* El acceso a Firebase queda separado.
* La pantalla no necesita saber cómo funciona Firebase.

---

## Paso 3: separar la lógica de tareas

Antes, la validación estaba dentro del componente.

Después, se mueve al hook:

```txt
src/hooks/useTasks.js
```

Resultado:

* La lógica queda reutilizable.
* El componente visual queda más limpio.
* La validación se puede probar mejor.

---

## Paso 4: separar el formulario

Antes, el formulario estaba dentro del componente monolítico.

Después, se mueve a:

```txt
src/components/TaskForm.js
```

Resultado:

* El formulario tiene su propio archivo.
* Solo se encarga de escribir y enviar texto.

---

## Paso 5: separar la lista

Antes, la lista estaba escrita directamente dentro del componente.

Después, se crea:

```txt
src/components/DataList.js
```

Resultado:

* La lista se puede reutilizar.
* Se aplica OCP porque se puede extender con props sin modificar su código.

---

## Paso 6: crear una página que una todo

La página final queda en:

```txt
src/pages/TasksPage.js
```

Esta página usa las piezas separadas:

* `useTasks`
* `TaskForm`
* `DataList`

Resultado:

* El código queda más claro.
* Cada archivo hace una sola cosa.
* La aplicación es más fácil de mantener.

---

# 8. Comparación entre la versión monolítica y la versión refactorizada

| Parte               | Monolítico            | Refactorizado    |
| ------------------- | --------------------- | ---------------- |
| Firebase            | Dentro del componente | `firebase.js`    |
| Lectura y escritura | Dentro del componente | `taskService.js` |
| Validación          | Dentro del componente | `useTasks.js`    |
| Formulario          | Dentro del componente | `TaskForm.js`    |
| Lista               | Dentro del componente | `DataList.js`    |
| Pantalla            | Todo mezclado         | `TasksPage.js`   |
| SRP                 | No se cumple          | Sí se cumple     |
| OCP                 | Débil                 | Mejor aplicado   |

---

# 9. Dónde se aplica SRP

SRP se aplica así:

| Archivo          | Responsabilidad única    |
| ---------------- | ------------------------ |
| `firebase.js`    | Conectar Firebase        |
| `taskService.js` | Leer y guardar tareas    |
| `useTasks.js`    | Manejar lógica de tareas |
| `TaskForm.js`    | Mostrar formulario       |
| `DataList.js`    | Mostrar listas           |
| `TasksPage.js`   | Armar la página          |
| `App.js`         | Cargar la aplicación     |

Cada archivo tiene una tarea clara.

Eso hace que el código sea más ordenado.

---

# 10. Dónde se aplica OCP

OCP se aplica principalmente en `DataList.js`.

Este componente no está cerrado a un solo tipo de dato.

Puede mostrar diferentes listas usando propiedades.

```js
<DataList
  items={tasks}
  renderItem={(task) => task.text}
  emptyText="No hay tareas"
/>
```

Si mañana se necesita mostrar una lista de productos, no se cambia `DataList.js`.

Solo se usa así:

```js
<DataList
  items={products}
  renderItem={(product) => product.name}
  emptyText="No hay productos"
/>
```

Eso es mejor porque se extiende el comportamiento sin modificar el componente original.

---

# 11. Explicación simple para recordar

## SRP

> Una cosa debe hacer una sola tarea.

Como un lápiz: sirve para escribir.

No debería ser lápiz, cuchara, pelota y zapato al mismo tiempo.

---

## OCP

> Debo poder agregar algo nuevo sin romper lo que ya funciona.

Como un juguete al que puedes ponerle accesorios sin desarmarlo.

---

# 12. Conclusión

La versión monolítica funciona, pero no está bien organizada porque un solo archivo hace demasiadas cosas.

La versión refactorizada es mejor porque:

* Separa responsabilidades.
* Facilita entender el código.
* Facilita corregir errores.
* Facilita agregar nuevas funciones.
* Aplica SRP dividiendo el código por responsabilidades.
* Aplica OCP usando componentes reutilizables como `DataList`.

La mejor práctica es construir aplicaciones con piezas pequeñas, claras y reutilizables.

Así el código se parece más a una caja ordenada de juguetes: cada cosa está en su lugar.
