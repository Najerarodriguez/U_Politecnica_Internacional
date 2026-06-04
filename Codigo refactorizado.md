# Análisis y Guía de Refactorización — App Contable

## 1. Objetivo de Aprendizaje

Refactorizar el componente `App.js` aplicando principios de **Clean Code**:
- Nombres que dicen claramente para qué sirven
- Funciones pequeñas con una sola tarea
- Eliminar comentarios innecesarios haciendo el código autoexplicativo

---

## 2. ¿Qué es refactorizar? 

Imagina que tienes una caja de juguetes donde metiste **todo revuelto**: calcetines, lápices, carros y galletas. Todo cabe, pero nadie sabe dónde está nada.

**Refactorizar** es ordenar esa caja sin quitar ni agregar juguetes. Al final los juguetes son exactamente los mismos, pero ahora cada cosa tiene su lugar y su nombre.

En código hacemos lo mismo: **no cambiamos lo que hace el programa**, solo lo hacemos más fácil de leer y entender.

---

## 3. Problema 1 — Nombres que no dicen nada

Si le pones a tu perro el nombre **"X"** nadie sabe que es un perro. Si le pones **"Firulais"** todos saben de quién hablas. Los nombres en el código funcionan igual.

### Lo que tiene el código ahora (malo)

```js
const [x, setX] = useState('login');        // ¿qué es x?
const [u, setU] = useState([]);             // ¿qué es u?
const [a, setA] = useState([]);             // ¿qué es a?
const [b, setB] = useState([]);             // ¿qué es b?
const [z, setZ] = useState(null);           // ¿qué es z?
const [f, setF] = useState({...});          // ¿qué es f?
const [c, setC] = useState({...});          // ¿qué es c?
const [d, setD] = useState({...});          // ¿qué es d?
const [q, setQ] = useState({...});          // ¿qué es q?
const [m, setM] = useState('');             // ¿qué es m?
```

Y las funciones:

```js
const h  = (e) => { ... }   // ¿qué hace h?
const h2 = (e) => { ... }   // ¿qué hace h2?
const h3 = (e) => { ... }   // ¿qué hace h3?
const h4 = (e) => { ... }   // ¿qué hace h4?
const kk = (modo) => { ... } // ¿qué hace kk?
const jj = (e) => { ... }    // ¿qué hace jj?
const ll = (e) => { ... }    // ¿qué hace ll?
```

### Cómo deberían llamarse (bueno)

| Nombre actual | Nombre correcto | Por qué |
|---|---|---|
| `x` | `vistaActual` | Guarda qué pantalla se muestra: `'login'` o `'dashboard'` |
| `u` | `usuarios` | Lista de usuarios registrados |
| `a` | `catalogoCuentas` | Lista de cuentas contables |
| `b` | `transacciones` | Lista de movimientos contables |
| `z` | `usuarioSesion` | El usuario que está conectado ahora |
| `f` | `formularioAuth` | Los campos del formulario de login/registro |
| `c` | `formularioCuenta` | Los campos para crear una cuenta contable |
| `d` | `formularioTransaccion` | Los campos para registrar una transacción |
| `q` | `filtros` | Los valores para filtrar la tabla |
| `m` | `mensajeError` | El texto de error o confirmación que se muestra |
| `h` | `actualizarFormularioAuth` | Actualiza los campos de login/registro |
| `h2` | `actualizarFormularioCuenta` | Actualiza los campos de cuenta contable |
| `h3` | `actualizarFormularioTransaccion` | Actualiza los campos de transacción |
| `h4` | `actualizarFiltros` | Actualiza los filtros del dashboard |
| `kk` | `procesarAutenticacion` | Decide si hace login o registro |
| `jj` | `guardarCuentaContable` | Guarda una cuenta nueva en el catálogo |
| `ll` | `guardarTransaccion` | Guarda una transacción nueva |

### Cómo se vería el código refactorizado

```js
// Antes
const [x, setX] = useState('login');

// Después
const [vistaActual, setVistaActual] = useState('login');
```

```js
// Antes
const h = (e) => {
  const { name, value } = e.target;
  setF({ ...f, [name]: value });
};

// Después
const actualizarFormularioAuth = (e) => {
  const { name, value } = e.target;
  setFormularioAuth({ ...formularioAuth, [name]: value });
};
```

Ahora cualquier persona que lea el código sabe exactamente qué hace cada variable y función, **sin necesidad de leer todo el archivo**.

---

## 4. Problema 2 — Funciones que hacen demasiadas cosas

Imagina que le pides a tu mamá que haga **una sola cosa**: "mamá, por favor tráeme agua". Eso es fácil.

Ahora imagina pedirle: "mamá, tráeme agua, lava los platos, llama a la abuela, busca mis zapatos y también haz la tarea". Eso es muy difícil de hacer bien y si algo sale mal no sabes qué parte falló.

Las funciones en el código deben ser como la primera petición: **hacer una sola cosa**.

### El problema en el código actual

La función `kk` (que deberíamos llamar `procesarAutenticacion`) hace **5 cosas a la vez**:

1. Valida que todos los campos estén llenos
2. Comprueba que las contraseñas coincidan
3. Verifica que el usuario no exista ya
4. Crea el objeto del nuevo usuario
5. Guarda en Firebase Y actualiza el estado local

La función `jj` (`guardarCuentaContable`) también hace **4 cosas**:

1. Valida los campos del formulario
2. Crea el objeto de la cuenta
3. Guarda la cuenta en Firebase
4. Actualiza el contador de catálogos del usuario (¡que es una tarea diferente!)

### Cómo debería quedar (funciones pequeñas con una sola tarea)

```js
// Una función solo para validar el registro
function validarFormularioRegistro(formulario) {
  if (!formulario.usuario || !formulario.correo ||
      !formulario.clave   || !formulario.confirmar ||
      !formulario.nombre  || !formulario.apellidos) {
    return 'Debes llenar todos los campos';
  }
  if (formulario.clave !== formulario.confirmar) {
    return 'Las contraseñas no coinciden';
  }
  return null; // null = sin errores
}

// Una función solo para verificar si el usuario ya existe
function usuarioYaExiste(usuarios, nuevoUsuario) {
  return usuarios.some(
    (u) => u.usuario === nuevoUsuario.usuario || u.correo === nuevoUsuario.correo
  );
}

// Una función solo para construir el objeto del nuevo usuario
function crearNuevoUsuario(formulario) {
  const hoy = new Date().toISOString().slice(0, 10);
  return {
    id: Date.now(),
    usuario:   formulario.usuario,
    correo:    formulario.correo,
    clave:     formulario.clave,
    nombre:    formulario.nombre,
    apellidos: formulario.apellidos,
    creado:    hoy,
    logins:    0,
    catalogos: 0,
    transacciones: 0,
    eventos: [{ fecha: hoy, tipo: 'registro', valor: 1 }]
  };
}

// Una función solo para registrar el usuario en Firebase
function guardarUsuarioEnFirebase(usuario) {
  return set(push(ref(db, 'usuarios_demo')), usuario);
}

// Ahora la función principal es fácil de leer porque delega cada tarea
function registrarUsuario() {
  const errorValidacion = validarFormularioRegistro(formularioAuth);
  if (errorValidacion) {
    setMensajeError(errorValidacion);
    return;
  }

  if (usuarioYaExiste(usuarios, formularioAuth)) {
    setMensajeError('El usuario o correo ya existe');
    return;
  }

  const nuevoUsuario = crearNuevoUsuario(formularioAuth);
  setUsuarios([...usuarios, nuevoUsuario]);
  guardarUsuarioEnFirebase(nuevoUsuario);
  setVistaActual('login');
  setMensajeError('Registro exitoso, ahora inicia sesión');
}
```

Cada función ahora hace **exactamente una cosa** y tiene un nombre que lo dice.

---

## 5. Problema 3 — Comentarios que no ayudan

Si en tu libro de matemáticas dice **"2 + 2 = 4"** y alguien escribe al lado **"esto suma dos con dos"**, ese comentario no ayuda. Ya se ve.

Pero si el libro dice algo difícil como **"∫x²dx = x³/3 + C"** y alguien escribe **"esta fórmula calcula el área bajo una curva"**, eso sí ayuda.

Los comentarios en el código solo deben existir cuando explican el **por qué** de algo difícil, no el **qué** de algo obvio.

### Comentarios malos en el código actual

```js
// esto guarda cosas
useEffect(() => {
  localStorage.setItem('usuariosxxx', JSON.stringify(u));
  ...
}, [u, a, b, z]);

// esto hace algo
const h = (e) => { ... };

// sale
const bye = () => { ... };
```

Estos comentarios no aportan nada. Si los nombres fueran correctos, el código se explica solo:

```js
// Sin comentario — el nombre lo dice todo
const cerrarSesion = () => {
  setUsuarioSesion(null);
  setVistaActual('login');
  setMensajeError('');
  setFormularioAuth({ usuario: '', clave: '', correo: '', confirmar: '', nombre: '', apellidos: '' });
};
```

### ¿Cuándo SÍ es válido un comentario?

Cuando hay una decisión técnica no obvia. Por ejemplo:

```js
// Firebase y localStorage se sincronizan por separado porque
// Firebase puede tardar y el usuario necesita ver datos de inmediato.
useEffect(() => {
  persistirDatosEnLocalStorage({ usuarios, catalogoCuentas, transacciones, usuarioSesion });
}, [usuarios, catalogoCuentas, transacciones, usuarioSesion]);
```

Ese comentario explica el **por qué**, no el **qué**.

---

## 6. Resumen: Reglas de Clean Code aplicadas

| Regla | Cómo aplicarla en este componente |
|---|---|
| **Nombres significativos** | Renombrar `x`, `u`, `a`, `b`, `z`, `h`, `kk`, `jj`, `ll` por nombres descriptivos |
| **Funciones pequeñas** | Dividir `procesarAutenticacion` y `guardarCuentaContable` en funciones de una sola responsabilidad |
| **Código autoexplicativo** | Eliminar comentarios obvios como `// esto hace algo` y `// sale`; conservar solo los que explican decisiones no evidentes |
| **Sin efectos secundarios ocultos** | `guardarCuentaContable` no debería también actualizar el contador del usuario; esa debe ser una función separada |

---

## 7. Checklist antes de entregar el refactor

- [ ] Cada variable de estado tiene un nombre que describe qué guarda
- [ ] Cada función tiene un nombre que empieza con un verbo y describe su tarea (`guardar`, `validar`, `actualizar`, `crear`)
- [ ] Ninguna función hace más de una cosa
- [ ] Los comentarios explican el *por qué*, no el *qué*
- [ ] El comportamiento de la aplicación es exactamente igual al original
