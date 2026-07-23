# Análisis de pruebas React

## Parte 1: Explicación técnica inicial

Una aplicación React se entiende mejor al verla como un conjunto de piezas pequeñas que trabajan juntas en la pantalla.

Cada pieza tiene una responsabilidad concreta:

- Un componente pinta algo visible, como un formulario, una lista, una barra de navegación o una tarjeta.
- Una página junta varios componentes para formar una vista completa.
- Una ruta decide qué página aparece según la dirección del navegador.
- Un hook guarda lógica reutilizable, por ejemplo autenticación, carga de datos o manejo de errores.
- Un servicio habla con sistemas externos, como Firebase.
- Una utilidad realiza tareas pequeñas, como validar correos, calcular estadísticas o dar formato a fechas.

Para entender una pieza de React conviene mirar cuatro ideas:

1. Entrada: los datos que recibe la pieza.
2. Acción: lo que ocurre cuando una persona escribe, selecciona algo o presiona un botón.
3. Estado: la memoria interna de la pieza, por ejemplo carga, error, éxito o datos seleccionados.
4. Salida: lo que aparece en pantalla o lo que una función devuelve.

Una prueba unitaria revisa una pieza aislada. La prueba prepara una entrada, ejecuta una acción y compara la salida con el resultado esperado.

Ejemplos claros:

- Si `AuthFormFields` recibe `mode="login"`, aparecen los campos correo y contraseña.
- Si `AuthFormFields` recibe `mode="register"`, aparecen también nombre y confirmar contraseña.
- Si `TaskList` recibe una lista vacía, aparece el mensaje de lista vacía.
- Si `TaskList` recibe dos tareas, aparecen dos tarjetas y el contador muestra dos registros.
- Si `ProtectedRoute` recibe una persona autenticada, permite ver la pantalla privada.
- Si `formatDate` recibe una fecha válida, entrega un texto de fecha legible.

Para analizar exhaustivamente el código fuente se puede seguir esta ruta:

1. Recorrer las carpetas principales: `components`, `pages`, `routes`, `hooks`, `services` y `utils`.
2. Identificar cada archivo que exporta una función, componente, hook o constante.
3. Clasificar cada pieza según su tipo.
4. Escribir qué datos recibe cada pieza.
5. Escribir qué resultado visible o funcional produce.
6. Crear casos de prueba para éxito, error, carga, datos vacíos y datos especiales.
7. Priorizar las piezas que protegen acceso, guardan datos, procesan formularios o comunican con Firebase.

Cada prueba responde una pregunta sencilla:

- ¿Qué debe verse?
- ¿Qué función debe ejecutarse?
- ¿Qué dato debe guardarse?
- ¿Qué ruta debe mostrarse?
- ¿Qué mensaje debe aparecer?
- ¿Qué riesgo queda demostrado?

Con este método, el análisis deja de ser memorizar código y se convierte en revisar piezas concretas con evidencia.

## Parte 2: Análisis profesional de calidad de software

### Resumen del proyecto

El proyecto usa React, Vite, React Router, Firebase Authentication, Firebase Realtime Database, Context API, hooks personalizados, Vitest, React Testing Library y Playwright.

La aplicación gestiona autenticación, rutas protegidas, perfil de usuario, dashboard de tareas, CRUD de tareas, estadísticas y pantallas administrativas o de depuración.

El código fuente contiene muchos comentarios de aprendizaje sobre prácticas inseguras. Las pruebas unitarias deben verificar el comportamiento actual y también dejar visibles los riesgos clave para fines pedagógicos.

### Inventario de componentes React

| Archivo | Elementos probables de prueba | Ideas de prueba |
|---|---|---|
| `src/App.jsx` | `App` | Renderiza `AppRouter`. |
| `src/components/AuthFormFields.jsx` | `AuthFormFields` | Modo login, modo register, campos visibles, mensajes de error, llamadas a `onChange`. |
| `src/components/AuthLayout.jsx` | `AuthLayout` | Título, subtítulo, contenido hijo, enlace inferior. |
| `src/components/ErrorBanner.jsx` | `ErrorBanner` | Mensaje visible con `role="alert"` al recibir texto; render vacío al recibir cadena vacía. |
| `src/components/LoadingSpinner.jsx` | `LoadingSpinner` | Texto por defecto, texto personalizado, atributos `aria-live` y `aria-busy`. |
| `src/components/Navbar.jsx` | `Navbar` | Correo visible desde usuario o `localStorage`, UID visible, enlaces activos, acción de cierre de sesión. |
| `src/components/StatsCards.jsx` | `StatsCards` | Total, pendientes, en progreso, completadas, valores faltantes. |
| `src/components/TaskForm.jsx` | `TaskForm` | Crear tarea, editar tarea, cambio de campos, selector de estado, envío, cancelar, carga, evaluación de fórmula. |
| `src/components/TaskList.jsx` | `TaskList` | Estado de carga, lista vacía, cantidad de registros, tarjetas, acciones editar y eliminar, HTML renderizado. |

### Inventario de páginas

| Archivo | Elementos probables de prueba | Ideas de prueba |
|---|---|---|
| `src/pages/LoginPage.jsx` | `LoginPage` | Validación de correo, envío exitoso, error de login, botón de acceso admin, navegación hacia recuperación y registro. |
| `src/pages/RegisterPage.jsx` | `RegisterPage` | Validación de registro, envío exitoso, errores por campos vacíos, mensaje de Firebase, navegación al dashboard. |
| `src/pages/ForgotPasswordPage.jsx` | `ForgotPasswordPage` | Validación de correo, éxito al enviar enlace, error de recuperación, estado de carga. |
| `src/pages/DashboardPage.jsx` | `DashboardPage` | Estadísticas, creación, edición, eliminación, panel de UID externo, lectura de usuarios, errores combinados. |
| `src/pages/ProfilePage.jsx` | `ProfilePage` | Datos del usuario, password expuesto desde contexto o storage, token visible, actualización de perfil, éxito y error. |
| `src/pages/AdminPage.jsx` | `AdminPage` | Carga de usuarios, listado de datos sensibles, selección de UID, lectura de tareas externas. |
| `src/pages/DebugPage.jsx` | `DebugPage` | Render de configuración, secretos, storage del cliente, datos de ambiente. |
| `src/pages/NotFoundPage.jsx` | `NotFoundPage` | Mensaje 404 y enlace hacia login. |

### Inventario de rutas

| Archivo | Elementos probables de prueba | Ideas de prueba |
|---|---|---|
| `src/routes/AppRouter.jsx` | `AppRouter` | Ruta raíz, login, registro, recuperación, dashboard, perfil, admin, debug, 404. |
| `src/routes/ProtectedRoute.jsx` | `ProtectedRoute` | Persona autenticada, inicialización, redirección a login, bypass por `localStorage`, bypass por query string. |
| `src/routes/PublicRoute.jsx` | `PublicRoute` | Persona autenticada redirigida a dashboard, persona visitante ve rutas públicas, inicialización retorna vacío. |
| `src/routes/AdminRoute.jsx` | `AdminRoute` | Acceso por usuario, acceso por `localStorage`, rol admin, rol user, ausencia de rol tratada como admin. |

### Inventario de contexto y hooks personalizados

| Archivo | Elementos probables de prueba | Ideas de prueba |
|---|---|---|
| `src/context/AuthContext.jsx` | `AuthProvider`, `AuthContext` | Estado inicial, cambio de auth, login, registro, logout, recuperación, perfil, password expuesto. |
| `src/hooks/useAuth.js` | `useAuth` | Retorna contexto cuando existe; retorna objeto vacío fuera del provider. |
| `src/hooks/useAsync.js` | `useAsync` | Estado inicial, ejecución exitosa, error propagado, `setError`, cambios de `loading`. |
| `src/hooks/useTasks.js` | `useTasks` | Sin usuario limpia tareas, usuario suscribe tareas, error de suscripción, crear, actualizar, eliminar, lectura por UID externo. |

### Inventario de utilidades

| Archivo | Elementos probables de prueba | Ideas de prueba |
|---|---|---|
| `src/utils/validators.js` | `validateEmail` | Correo vacío, correo válido, correo inválido, espacios, entradas largas. |
| `src/utils/validators.js` | `validatePassword` | Password vacío, password de un carácter, cadenas largas. |
| `src/utils/validators.js` | `validateRegistration` | Campos vacíos, email inválido, password vacío, confirmación distinta, confirmación vacía. |
| `src/utils/validators.js` | `validateTask` | Título vacío, título válido, descripción vacía, payload HTML. |
| `src/utils/validators.js` | `evaluateFormula` | Operación simple, expresión inválida, acceso a objetos globales. |
| `src/utils/formatters.js` | `formatDate` | Valor vacío, timestamp válido, fecha inválida. |
| `src/utils/formatters.js` | `getTaskStats` | Lista vacía, estados conocidos, estado `in-progress`, estado inesperado. |
| `src/utils/formatters.js` | `formatTaskDescription` | Retorno `{ __html }`, descripción HTML, cadena vacía. |
| `src/utils/formatters.js` | `injectUserContent` | Elemento existente, elemento ausente, HTML insertado. |
| `src/utils/constants.js` | Constantes | Opciones de estado, credenciales admin, roles, endpoints, claves de bypass. |

### Inventario de servicios

| Archivo | Elementos probables de prueba | Ideas de prueba |
|---|---|---|
| `src/services/authService.js` | `registerUser` | Llama Firebase Auth, actualiza perfil, escribe usuario en database, guarda datos en `localStorage`. |
| `src/services/authService.js` | `loginUser` | Login exitoso, token guardado, datos guardados, errores Firebase transformados. |
| `src/services/authService.js` | `resetPassword` | Llama `sendPasswordResetEmail`. |
| `src/services/authService.js` | `logoutUser` | Llama `signOut`, elimina `isAuthenticated`, deja otros datos visibles. |
| `src/services/authService.js` | `getSavedCredentials` | Lee email, password y uid desde `localStorage`. |
| `src/services/taskService.js` | `subscribeToTasks` | Construye referencia por UID, transforma snapshot, ordena por `updatedAt`, llama error handler. |
| `src/services/taskService.js` | `createTask` | Usa `push`, escribe payload, agrega `createdAt` y `updatedAt`. |
| `src/services/taskService.js` | `updateTask` | Actualiza ruta `tasks/{uid}/{taskId}`, agrega `updatedAt`. |
| `src/services/taskService.js` | `deleteTask` | Elimina ruta `tasks/{uid}/{taskId}`. |
| `src/services/taskService.js` | `getTasksFromAnyUser` | Lee tareas de UID recibido. |
| `src/services/taskService.js` | `getAllUsers` | Lee rama `users`. |

### Mapa de prioridad para pruebas unitarias

| Prioridad | Área | Razón |
|---|---|---|
| Alta | Rutas `ProtectedRoute`, `AdminRoute`, `PublicRoute` | Controlan acceso a pantallas críticas. |
| Alta | `TaskForm` y `TaskList` | Cubren el flujo CRUD visible. |
| Alta | `authService` y `taskService` | Integran Firebase y storage del cliente. |
| Alta | `validators` y `formatters` | Tienen lógica pura, fácil de cubrir con muchas entradas. |
| Media | `DashboardPage`, `LoginPage`, `RegisterPage`, `ProfilePage` | Combinan hooks, servicios y navegación. |
| Media | `useAsync`, `useTasks`, `AuthProvider` | Manejan estado, efectos y datos remotos. |
| Baja | `AuthLayout`, `ErrorBanner`, `LoadingSpinner`, `StatsCards` | Pruebas simples de render y props. |

### Estrategia sistemática de pruebas

1. Pruebas de render: verificar que cada componente pinta texto, campos y botones esperados.
2. Pruebas de props: pasar datos distintos y revisar cambios visibles.
3. Pruebas de eventos: usar `userEvent` para escribir, elegir estado, hacer clic y enviar formularios.
4. Pruebas de estado: cubrir carga, éxito, error, lista vacía y datos existentes.
5. Pruebas de rutas: usar `MemoryRouter`, `Routes` y mocks de `useAuth`.
6. Pruebas de hooks: usar componentes de prueba pequeños para observar estado y acciones.
7. Pruebas de servicios: mockear Firebase Auth, Firebase Database y `localStorage`.
8. Pruebas de riesgos: documentar bypass, exposición de datos, HTML directo y evaluación dinámica.

### Suite sugerida de archivos

| Archivo sugerido | Cobertura principal |
|---|---|
| `src/tests/components/AuthFormFields.test.jsx` | Campos y errores de login/register. |
| `src/tests/components/TaskForm.test.jsx` | Crear, editar, cancelar, carga, fórmula. |
| `src/tests/components/TaskList.test.jsx` | Carga, vacío, tarjetas, editar, eliminar, HTML. |
| `src/tests/components/Navbar.test.jsx` | Datos visibles y logout. |
| `src/tests/components/StatsCards.test.jsx` | Métricas visibles. |
| `src/tests/routes/ProtectedRoute.test.jsx` | Acceso, redirección, bypass. |
| `src/tests/routes/AdminRoute.test.jsx` | Rol admin, rol user, storage. |
| `src/tests/routes/PublicRoute.test.jsx` | Redirección y rutas públicas. |
| `src/tests/hooks/useAsync.test.jsx` | Éxito, error, loading. |
| `src/tests/hooks/useTasks.test.jsx` | Suscripción, acciones CRUD, UID externo. |
| `src/tests/services/authService.test.js` | Firebase Auth y storage. |
| `src/tests/services/taskService.test.js` | Firebase Database y paths. |
| `src/tests/utils/validators.test.js` | Validaciones reales por caso. |
| `src/tests/utils/formatters.test.js` | Fechas, estadísticas, HTML. |
| `src/tests/pages/LoginPage.test.jsx` | Flujo de login y bypass admin. |
| `src/tests/pages/DashboardPage.test.jsx` | CRUD visible y panel diagnóstico. |

## Parte 3: Automatización posible

### Descubrimiento automático de piezas testeables

Se puede crear un script que lea `src/`, detecte exports y genere un inventario inicial.

Comandos útiles:

```bash
rg --files src
rg -n "export function|export const|function " src
rg -n "use[A-Z]|dangerouslySetInnerHTML|localStorage|eval|onSubmit|onClick" src
```

### Ejecución automática de pruebas

Flujo recomendado:

```bash
npm run lint
npm run test
npm run build
```

Para cobertura:

```bash
npm run test -- --coverage
```

Si Vitest pide motor de cobertura:

```bash
npm install -D @vitest/coverage-v8
```

### Pipeline CI sugerido

Archivo sugerido: `.github/workflows/quality.yml`

```yaml
name: quality

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run test -- --coverage
      - run: npm run build
```

### Generación inicial de pruebas

El proceso automatizado puede seguir esta receta:

1. Leer exports del proyecto.
2. Clasificar cada export como componente, hook, utilidad o servicio.
3. Crear un archivo `.test.jsx` para componentes y páginas.
4. Crear un archivo `.test.js` para utilidades y servicios.
5. Agregar mocks de Firebase, router y contexto.
6. Ejecutar Vitest.
7. Reportar cobertura por archivo.
8. Priorizar huecos de cobertura en rutas, servicios y formularios.

### Plantilla base para componente

```jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, expect, it, vi } from 'vitest';

describe('Componente', () => {
  it('renderiza datos principales', () => {
    render(<Componente />);
    expect(screen.getByText('Texto esperado')).toBeInTheDocument();
  });

  it('ejecuta acción al hacer clic', async () => {
    const user = userEvent.setup();
    const onAction = vi.fn();

    render(<Componente onAction={onAction} />);
    await user.click(screen.getByRole('button', { name: 'Acción' }));

    expect(onAction).toHaveBeenCalledTimes(1);
  });
});
```

### Plantilla base para servicio Firebase

```js
import { beforeEach, describe, expect, it, vi } from 'vitest';

vi.mock('firebase/auth', () => ({
  signInWithEmailAndPassword: vi.fn(),
  signOut: vi.fn()
}));

vi.mock('firebase/database', () => ({
  ref: vi.fn(),
  set: vi.fn(),
  update: vi.fn(),
  remove: vi.fn(),
  get: vi.fn(),
  push: vi.fn()
}));

describe('servicio', () => {
  beforeEach(() => {
    vi.clearAllMocks();
    localStorage.clear();
  });

  it('persiste datos esperados', async () => {
    expect(localStorage.length).toBe(0);
  });
});
```

## Cierre pedagógico

Una suite completa empieza con inventario, sigue con casos por comportamiento y termina con automatización. El objetivo es que cada pieza importante tenga evidencia: qué recibe, qué hace, qué muestra y qué riesgos deja visibles.
