# Guía para implementar una suite sencilla de pruebas unitarias

## Objetivo

Esta guía explica cómo crear una suite básica de pruebas unitarias para componentes React del proyecto.

La idea central es sencilla:

- Elegir una pieza pequeña de la pantalla.
- Darle datos de ejemplo.
- Revisar qué aparece.
- Confirmar que React muestra el resultado esperado.

Las dos primeras pruebas serán fáciles de observar:

1. Verificar que un mensaje de error aparece en pantalla.
2. Verificar que unas tarjetas de estadísticas muestran sus números.

## Qué es una prueba unitaria

Una prueba unitaria revisa una pieza pequeña del código.

En React, esa pieza puede ser:

- Un componente visual.
- Una función que calcula datos.
- Un formulario.
- Una lista.
- Una ruta.

En esta guía se prueban componentes visuales porque permiten ver con claridad qué está sucediendo.

## Herramientas usadas

El proyecto ya tiene estas herramientas instaladas en `package.json`:

- `vitest`: ejecuta las pruebas.
- `@testing-library/react`: renderiza componentes React durante la prueba.
- `@testing-library/jest-dom`: agrega frases de prueba como `toBeInTheDocument`.
- `jsdom`: simula un navegador dentro de la prueba.

## Dónde van las pruebas

Las pruebas del proyecto están en:

```text
src/tests/
```

Para mantener orden, se puede crear este archivo:

```text
src/tests/componentes-basicos.test.jsx
```

## Paso 1: Crear el archivo de pruebas

Crear el archivo:

```text
src/tests/componentes-basicos.test.jsx
```

## Paso 2: Escribir el contenido inicial

Pegar este código dentro del archivo:

```jsx
import { render, screen } from '@testing-library/react';
import { describe, expect, it } from 'vitest';
import { ErrorBanner } from '../components/ErrorBanner';
import { StatsCards } from '../components/StatsCards';

describe('Componentes basicos', () => {
  it('muestra un mensaje de error visible', () => {
    render(<ErrorBanner message="Algo salio mal" />);

    expect(screen.getByRole('alert')).toBeInTheDocument();
    expect(screen.getByText('Algo salio mal')).toBeInTheDocument();
  });

  it('muestra las estadisticas de tareas', () => {
    const stats = {
      total: 4,
      pending: 2,
      'in-progress': 1,
      completed: 1
    };

    render(<StatsCards stats={stats} />);

    expect(screen.getByText('Total')).toBeInTheDocument();
    expect(screen.getByText('4')).toBeInTheDocument();
    expect(screen.getByText('Pendientes')).toBeInTheDocument();
    expect(screen.getByText('2')).toBeInTheDocument();
    expect(screen.getByText('En progreso')).toBeInTheDocument();
    expect(screen.getAllByText('1')).toHaveLength(2);
    expect(screen.getByText('Completadas')).toBeInTheDocument();
  });
});
```

## Qué sucede en la primera prueba

La primera prueba usa este componente:

```jsx
<ErrorBanner message="Algo salio mal" />
```

Ese componente recibe un texto por medio de `message`.

La prueba hace esto:

1. `render(...)` coloca el componente en una pantalla simulada.
2. `screen.getByRole('alert')` busca el contenedor de alerta.
3. `screen.getByText('Algo salio mal')` busca el mensaje visible.
4. `expect(...).toBeInTheDocument()` confirma que cada elemento aparece.

La persona puede imaginarlo así:

```text
Entrada:
message = "Algo salio mal"

Resultado esperado:
En pantalla aparece una alerta con el texto "Algo salio mal".
```

## Qué sucede en la segunda prueba

La segunda prueba usa este componente:

```jsx
<StatsCards stats={stats} />
```

El componente recibe estos datos:

```js
const stats = {
  total: 4,
  pending: 2,
  'in-progress': 1,
  completed: 1
};
```

La prueba revisa que aparezcan:

- El título `Total`.
- El número `4`.
- El título `Pendientes`.
- El número `2`.
- El título `En progreso`.
- Dos números `1`, uno para tareas en progreso y otro para tareas completadas.
- El título `Completadas`.

La persona puede visualizarlo así:

```text
Entrada:
4 tareas en total
2 pendientes
1 en progreso
1 completada

Resultado esperado:
Las tarjetas muestran esos mismos valores en pantalla.
```

## Paso 3: Ejecutar las pruebas

Desde la terminal, dentro del proyecto, ejecutar:

```bash
npm test
```

También se puede ejecutar Vitest en modo interactivo:

```bash
npm run test:watch
```

## Cómo leer el resultado

Cuando las pruebas pasan, Vitest muestra un resumen parecido a:

```text
✓ muestra un mensaje de error visible
✓ muestra las estadisticas de tareas
```

Eso significa que los componentes mostraron lo esperado.

Si una prueba falla, Vitest muestra:

- El nombre de la prueba.
- La línea con el problema.
- Lo que buscaba.
- Lo que encontró.

Ese mensaje sirve como pista para corregir el componente o corregir la prueba.

## Paso 4: Entender el patrón

Cada prueba sigue el mismo patrón:

```text
Preparar -> Mostrar -> Revisar
```

En código:

```jsx
render(<Componente dato="valor" />);
expect(screen.getByText('valor')).toBeInTheDocument();
```

Explicado de forma directa:

1. Se prepara el componente con datos.
2. Se muestra en una pantalla simulada.
3. Se busca el texto o botón esperado.
4. Se confirma que aparece.

## Paso 5: Agregar más pruebas siguiendo la misma idea

Después de estas dos pruebas, se pueden agregar pruebas para:

- `LoadingSpinner`: revisar que aparezca `Cargando...`.
- `AuthFormFields`: revisar campos de login y registro.
- `TaskList`: revisar lista vacía y lista con tareas.
- `TaskForm`: revisar escritura en campos y envío.
- `ProtectedRoute`: revisar acceso según autenticación.

## Ejemplo adicional muy sencillo

Este ejemplo prueba el spinner de carga:

```jsx
import { render, screen } from '@testing-library/react';
import { describe, expect, it } from 'vitest';
import { LoadingSpinner } from '../components/LoadingSpinner';

describe('LoadingSpinner', () => {
  it('muestra el texto de carga', () => {
    render(<LoadingSpinner />);

    expect(screen.getByText('Cargando...')).toBeInTheDocument();
  });
});
```

Visualmente significa:

```text
Entrada:
Componente LoadingSpinner

Resultado esperado:
Aparece el texto "Cargando...".
```

## Recomendación para aprender

Para aprender con claridad, conviene empezar por componentes que muestran texto. Luego avanzar hacia formularios, clics, rutas, hooks y servicios.

Orden sugerido:

1. Componentes visuales simples.
2. Listas y tarjetas.
3. Formularios.
4. Rutas.
5. Hooks.
6. Servicios con Firebase simulado mediante mocks.

## Cierre

Una suite de pruebas unitarias comienza con casos pequeños y fáciles de leer. Las pruebas de esta guía ayudan a ver la relación entre datos de entrada y resultado visible en pantalla.
