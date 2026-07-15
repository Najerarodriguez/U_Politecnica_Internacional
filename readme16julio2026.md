# Refactorización de React + Firebase aplicando SRP y OCP

## Objetivo

Implementar los principios de **Responsabilidad Única (SRP)** y **Abierto/Cerrado (OCP)** en una aplicación React conectada a Firebase Realtime Database.

El ejemplo utiliza una lista de tareas para mostrar dos versiones:

1. Un componente monolítico que mezcla presentación, lógica de negocio y acceso a datos.
2. Una versión refactorizada que separa esas responsabilidades y permite extender componentes sin modificarlos.

Este proyecto asume una aplicación existente creada con **Create React App**, no con Vite.

---

# Parte 1: componente monolítico

El siguiente componente funciona, pero viola intencionalmente SRP porque concentra demasiadas responsabilidades en un solo archivo.

## `src/TasksMonolith.js`

```jsx
import { useEffect, useState } from "react";
import { initializeApp } from "firebase/app";
import { getDatabase, onValue, push, ref } from "firebase/database";

const firebaseConfig = {
  apiKey: "AIzaSyD2kzJwTVJfd_4vYtI8pqE0zQ1xxqiKvOo",
  authDomain: "malas-practicas.firebaseapp.com",
  databaseURL: "https://malas-practicas-default-rtdb.firebaseio.com",
  projectId: "malas-practicas",
  storageBucket: "malas-practicas.firebasestorage.app",
  messagingSenderId: "1077058440703",
  appId: "1:1077058440703:web:b56d7ef5575e5e5becabca",
  measurementId: "G-7S327PFC74",
};

const db = getDatabase(initializeApp(firebaseConfig));

export default function TasksMonolith() {
  const [text, setText] = useState("");
  const [tasks, setTasks] = useState([]);

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
}
```

## ¿Por qué este componente viola SRP?

El componente tiene varias razones diferentes para cambiar:

| Responsabilidad               | Código involucrado                 |
| ----------------------------- | ---------------------------------- |
| Configurar Firebase           | `firebaseConfig` e `initializeApp` |
| Acceder a la base de datos    | `onValue`, `push` y `ref`          |
| Transformar datos de Firebase | `Object.entries`                   |
| Aplicar reglas de negocio     | Evitar tareas vacías               |
| Administrar estado            | `useState` y `useEffect`           |
| Mostrar la interfaz           | Formulario, lista y elementos HTML |

Por ejemplo:

* Si cambia el diseño, debe modificarse este archivo.
* Si cambia la validación, debe modificarse este archivo.
* Si cambia la ruta de Firebase, debe modificarse este archivo.
* Si Firebase se reemplaza por otra API, debe modificarse este archivo.

El problema no es que el componente tenga muchas líneas. El problema es que contiene **responsabilidades que cambian por motivos distintos**.

---

# Parte 2: refactorización aplicando SRP y OCP

## Estructura final

```text
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

El flujo de dependencias será:

```text
Componentes → Hook → Servicio → Firebase
```

Cada capa conoce únicamente la capa que necesita utilizar.

---

## Paso 1: separar la configuración de Firebase

## `src/firebase.js`

```js
import { initializeApp } from "firebase/app";
import { getDatabase } from "firebase/database";

const firebaseConfig = {
  apiKey: "AIzaSyD2kzJwTVJfd_4vYtI8pqE0zQ1xxqiKvOo",
  authDomain: "malas-practicas.firebaseapp.com",
  databaseURL: "https://malas-practicas-default-rtdb.firebaseio.com",
  projectId: "malas-practicas",
  storageBucket: "malas-practicas.firebasestorage.app",
  messagingSenderId: "1077058440703",
  appId: "1:1077058440703:web:b56d7ef5575e5e5becabca",
  measurementId: "G-7S327PFC74",
};

const app = initializeApp(firebaseConfig);

export const db = getDatabase(app);
```

Este archivo tiene una sola responsabilidad: **inicializar y exportar la conexión con Firebase**.

Google Analytics no se inicializa porque no participa en el caso de uso de tareas. Importar SDKs que no se utilizan agrega código y dependencias innecesarias.

---

## Paso 2: crear el servicio de acceso a datos

## `src/services/taskService.js`

```js
import { onValue, push, ref } from "firebase/database";
import { db } from "../firebase";

const tasksRef = ref(db, "tasks");

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

El servicio se encarga exclusivamente de:

* Conocer la ruta `tasks`.
* Leer información desde Firebase.
* Transformar la estructura recibida.
* Guardar información en Firebase.

El servicio no contiene JSX, no utiliza estado de React y no conoce cómo se muestran las tareas.

`onValue` ejecuta el callback con los datos iniciales y vuelve a ejecutarlo cuando existen cambios. También devuelve una función para cancelar la suscripción, lo que permite limpiar correctamente el listener cuando el componente se desmonta. ([Firebase][2])

---

## Paso 3: mover la lógica de negocio a un hook personalizado

## `src/hooks/useTasks.js`

```js
import { useEffect, useState } from "react";
import {
  createTask,
  subscribeTasks,
} from "../services/taskService";

export function useTasks() {
  const [tasks, setTasks] = useState([]);

  useEffect(() => subscribeTasks(setTasks), []);

  const addTask = async (rawText) => {
    const text = rawText.trim();

    if (!text) return false;

    await createTask({
      text,
      createdAt: Date.now(),
    });

    return true;
  };

  return {
    tasks,
    addTask,
  };
}
```

El hook administra:

* El estado de las tareas.
* La suscripción a los datos.
* La regla de negocio que impide guardar tareas vacías.
* La creación del objeto que será almacenado.

El hook no contiene elementos HTML y no conoce los detalles internos de Firebase.

La devolución de `subscribeTasks` desde `useEffect` hace que React ejecute automáticamente la función de desuscripción cuando el componente se desmonta.

---

## Paso 4: crear un componente de presentación

## `src/components/TaskForm.js`

```jsx
import { useState } from "react";

export default function TaskForm({ onAdd }) {
  const [text, setText] = useState("");

  const submit = async (event) => {
    event.preventDefault();

    if (await onAdd(text)) {
      setText("");
    }
  };

  return (
    <form onSubmit={submit}>
      <input
        value={text}
        onChange={(event) => setText(event.target.value)}
        placeholder="Nueva tarea"
      />

      <button>Agregar</button>
    </form>
  );
}
```

Este componente solamente se encarga de la interacción visual:

* Mostrar el campo de texto.
* Capturar lo escrito.
* Ejecutar la función recibida mediante `onAdd`.
* Limpiar el campo cuando la operación es válida.

No importa Firebase, no conoce la ruta de la base de datos y no decide las reglas para crear una tarea.

El estado `text` pertenece al componente porque es **estado visual del formulario**, no una regla de negocio.

---

## Paso 5: aplicar OCP con un componente extensible

## `src/components/DataList.js`

```jsx
export default function DataList({
  items,
  renderItem,
  getKey = (item) => item.id,
  emptyText = "No hay elementos",
}) {
  if (!items.length) {
    return <p>{emptyText}</p>;
  }

  return (
    <ul>
      {items.map((item) => (
        <li key={getKey(item)}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}
```

Este componente aplica OCP porque su comportamiento puede extenderse mediante propiedades:

* `items`: datos que debe mostrar.
* `renderItem`: define cómo mostrar cada elemento.
* `getKey`: define cómo obtener la clave única.
* `emptyText`: personaliza el mensaje sin resultados.

El componente no necesita modificarse para mostrar tareas, usuarios, productos u otras entidades.

### Ejemplo con tareas

```jsx
<DataList
  items={tasks}
  renderItem={(task) => task.text}
  emptyText="No hay tareas"
/>
```

### Ejemplo con usuarios

```jsx
<DataList
  items={users}
  getKey={(user) => user.uid}
  renderItem={(user) => <strong>{user.name}</strong>}
  emptyText="No hay usuarios"
/>
```

En el segundo caso se agrega un nuevo uso sin cambiar el código original de `DataList`. Esto representa el principio **Abierto/Cerrado**:

* Abierto para agregar nuevos comportamientos.
* Cerrado a modificaciones constantes del componente estable.

---

## Paso 6: integrar las responsabilidades

## `src/pages/TasksPage.js`

```jsx
import DataList from "../components/DataList";
import TaskForm from "../components/TaskForm";
import { useTasks } from "../hooks/useTasks";

export default function TasksPage() {
  const { tasks, addTask } = useTasks();

  return (
    <main>
      <h1>Tareas</h1>

      <TaskForm onAdd={addTask} />

      <DataList
        items={tasks}
        renderItem={(task) => task.text}
        emptyText="No hay tareas"
      />
    </main>
  );
}
```

`TasksPage` funciona como componente de composición:

* Obtiene datos y acciones desde `useTasks`.
* Entrega la acción al formulario.
* Entrega los datos al listado.

No accede directamente a Firebase ni implementa validaciones.

---

## Paso 7: utilizar la página en la aplicación

## `src/App.js`

```jsx
import TasksPage from "./pages/TasksPage";

export default function App() {
  return <TasksPage />;
}
```

Para ejecutar la aplicación:

```bash
npm start
```

---

# ¿Qué es SRP?

**SRP**, o principio de Responsabilidad Única, establece que una unidad de código debe tener una sola razón principal para cambiar.

No significa necesariamente:

* Un archivo por cada función.
* Un componente de pocas líneas.
* Separar todo en la mayor cantidad posible de carpetas.

Significa agrupar únicamente el código que cambia por el mismo motivo.

En esta solución:

| Archivo          | Razón para cambiar                         |
| ---------------- | ------------------------------------------ |
| `firebase.js`    | Cambia la configuración de Firebase        |
| `taskService.js` | Cambia el acceso o estructura de datos     |
| `useTasks.js`    | Cambian las reglas relacionadas con tareas |
| `TaskForm.js`    | Cambia el formulario                       |
| `DataList.js`    | Cambia la forma general de mostrar listas  |
| `TasksPage.js`   | Cambia la composición de la página         |

## ¿Por qué es importante?

Una separación correcta permite:

* Cambiar la interfaz sin alterar Firebase.
* Cambiar Firebase sin reescribir los componentes.
* Reutilizar la lógica de tareas en otras páginas.
* Probar reglas de negocio con menos dependencias.
* Identificar más rápidamente dónde debe realizarse un cambio.
* Reducir el riesgo de afectar funcionalidades no relacionadas.

## ¿Cuándo aplicar SRP?

Debe considerarse especialmente cuando:

* Un componente contiene llamadas a APIs y mucho JSX.
* Una misma regla se utiliza en varias pantallas.
* El acceso a datos cambia independientemente de la interfaz.
* Los componentes son difíciles de probar.
* El archivo cambia frecuentemente por motivos diferentes.
* Se espera reemplazar Firebase por otra fuente de datos.

### Ejemplo

En una tienda en línea pueden separarse:

```text
ProductCard       → presentación
useCart           → reglas del carrito
productService    → acceso a productos
```

## ¿Cuándo no debe forzarse?

No conviene dividir código únicamente para aumentar la cantidad de archivos.

Un componente pequeño puede conservar:

* Estado estrictamente visual.
* Eventos simples.
* Formato exclusivo de ese componente.

Una separación es innecesaria cuando la nueva capa:

* No tiene una responsabilidad real.
* Solo llama otra función sin agregar valor.
* Hace más difícil seguir el flujo.
* Nunca se reutilizará ni cambiará de forma independiente.

La meta de SRP no es crear más archivos. La meta es reducir el acoplamiento entre responsabilidades diferentes.

---

# ¿Qué es OCP?

**OCP**, o principio Abierto/Cerrado, establece que una unidad de código debe permitir nuevos comportamientos sin exigir modificaciones constantes en su implementación estable.

No significa que el código nunca pueda modificarse. Significa que una nueva variante normal debería agregarse mediante:

* Propiedades.
* Composición.
* Funciones de estrategia.
* Callbacks.
* Nuevos componentes.
* Implementaciones intercambiables.

En este proyecto, `DataList` no contiene condiciones como:

```js
if (type === "task") {
  // ...
}

if (type === "user") {
  // ...
}

if (type === "product") {
  // ...
}
```

En su lugar, recibe `renderItem` y `getKey`. Cada consumidor define el comportamiento que necesita.

## ¿Por qué es importante?

OCP permite:

* Agregar variantes sin romper las existentes.
* Evitar componentes llenos de condiciones.
* Reutilizar comportamientos estables.
* Reducir regresiones.
* Facilitar la incorporación de nuevos requerimientos.

## ¿Cuándo aplicar OCP?

Es útil cuando:

* Existen varias maneras de mostrar una entidad.
* Un componente debe funcionar con diferentes tipos de datos.
* Se esperan nuevos proveedores o implementaciones.
* Una operación puede utilizar algoritmos diferentes.
* El código contiene múltiples condiciones basadas en tipos.

### Ejemplos

* Una lista configurable mediante `renderItem`.
* Un botón configurable mediante propiedades.
* Un sistema de pagos con distintas estrategias.
* Un sistema de notificaciones con correo, SMS o notificaciones push.
* Una tabla que recibe columnas mediante configuración.

## ¿Cuándo no debe forzarse?

No conviene crear abstracciones extensibles cuando:

* Solo existe un caso conocido.
* No hay una variación razonablemente esperada.
* La configuración resulta más compleja que el código original.
* La abstracción requiere demasiadas propiedades opcionales.
* Los casos realmente no comparten el mismo comportamiento.

Primero debe existir una necesidad concreta. Crear extensibilidad especulativa puede producir código más difícil de entender que la solución directa.

---

# Flujo completo de una tarea

Cuando el usuario agrega una tarea sucede lo siguiente:

1. `TaskForm` captura el texto.
2. `TaskForm` ejecuta `onAdd`.
3. `TasksPage` había conectado `onAdd` con `addTask`.
4. `useTasks` elimina espacios y valida que el texto no esté vacío.
5. `useTasks` llama a `createTask`.
6. `taskService` guarda la información en Firebase.
7. Firebase notifica el cambio mediante `onValue`.
8. `taskService` transforma los datos recibidos.
9. `useTasks` actualiza el estado.
10. React vuelve a renderizar `DataList`.

Cada parte ejecuta únicamente su responsabilidad.

---

# Comparación final

| Aspecto           | Componente monolítico            | Versión refactorizada                            |
| ----------------- | -------------------------------- | ------------------------------------------------ |
| Presentación      | Mezclada con Firebase            | Separada en componentes                          |
| Reglas de negocio | Dentro del componente            | Dentro de `useTasks`                             |
| Acceso a datos    | Dentro del componente            | Dentro de `taskService`                          |
| Configuración     | Duplicable y acoplada            | Centralizada en `firebase.js`                    |
| Reutilización     | Baja                             | Componentes y hook reutilizables                 |
| Extensibilidad    | Requiere modificar el componente | Se extiende mediante propiedades                 |
| Pruebas           | Requieren múltiples dependencias | Cada responsabilidad puede probarse por separado |
| Riesgo de cambios | Alto                             | Más localizado                                   |

---

# Verificación

La refactorización puede considerarse correcta cuando:

* Una tarea vacía no se guarda.
* Una tarea válida aparece en la lista.
* Los cambios de Firebase se reflejan en tiempo real.
* Ningún componente de presentación importa Firebase.
* El servicio no importa React.
* Las validaciones están fuera de los componentes visuales.
* `DataList` puede mostrar otra entidad sin modificar su código.
* El listener de Firebase se elimina cuando la página se desmonta.

---

# Consideraciones de producción

El ejemplo mantiene el código mínimo para concentrarse en SRP y OCP. En una aplicación real también deberían incorporarse:

* Estados de carga.
* Manejo de errores.
* Autenticación.
* Validaciones más completas.
* Reglas de seguridad.
* Pruebas unitarias.
* Firebase Local Emulator Suite para desarrollo.
* Marcas de tiempo generadas por el servidor cuando el orden cronológico sea importante.

Si las reglas de Realtime Database no permiten acceso, Firebase devolverá un error de permisos. No deben habilitarse reglas públicas permanentes únicamente para hacer funcionar el ejemplo; Firebase advierte que el acceso público permite que cualquier persona acceda a la base de datos. ([Firebase][3])

---

# Seguridad de la configuración de Firebase

La configuración web de Firebase identifica el proyecto, pero no reemplaza la autorización. Las claves web de Firebase están diseñadas para estar presentes en aplicaciones cliente; la protección real de los datos depende principalmente de Firebase Security Rules, autenticación, restricciones de API y App Check. ([Firebase][4])

Mover estos valores a variables de entorno puede ayudar a administrar diferentes ambientes, pero no convierte la configuración en un secreto porque cualquier valor utilizado por una aplicación React cliente termina incluido en el código enviado al navegador.

En Create React App, las variables de entorno personalizadas deben utilizar el prefijo:

```text
REACT_APP_
```

Nunca deben colocarse en el frontend:

* Claves privadas de cuentas de servicio.
* Credenciales de Firebase Admin SDK.
* Secretos de APIs privadas.
* Tokens con privilegios administrativos.

---
