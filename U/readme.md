# Hotel Guanacaste - Explicación del Sistema

## ¿Cómo ejecutar el sistema?

1. Abrir Visual Studio 2026.
2. Ir a **Archivo > Abrir > Proyecto o Solución** y buscar el archivo `Proyecto1-304010957.slnx` dentro de la carpeta `Proyecto1-304010957`.
3. Esperar a que Visual Studio cargue todo.
4. Presionar el botón verde de **Play** (o presionar **F5**).
5. Se abrirá el navegador con la página de inicio del Hotel Guanacaste.
6. Desde ahí se puede navegar a Clientes, Empleados, Habitaciones y Reservaciones usando las tarjetas de la página principal.

Cuando se cierra la aplicación, todos los datos se pierden porque están guardados en memoria, no en una base de datos.

---

## ¿Por qué las carpetas están organizadas así?

Imaginemos que el proyecto es una pizzería. Para que funcione bien, necesitamos tres equipos de personas que trabajan juntos:

- **El equipo de cocina** decide qué ingredientes lleva cada pizza. En el proyecto, eso son los **Models** (Modelos). Ellos definen cómo se ve un cliente, un empleado, una habitación o una reservación. Por ejemplo, dicen "un cliente tiene nombre, apellido y cédula", igual que un recetario dice "una pizza lleva masa, salsa y queso".

- **El equipo de meseros** recibe los pedidos de los clientes y los lleva a la cocina. En el proyecto, esos son los **Controllers** (Controladores). Cuando alguien en la página web hace clic en "Nuevo Cliente", el controlador es el mesero que recibe ese pedido, lo procesa y decide qué hacer con él.

- **El equipo de decoración** se encarga de que el restaurante se vea bonito y que los menús sean fáciles de leer. En el proyecto, esas son las **Views** (Vistas). Son las páginas que la persona ve en el navegador: los formularios, las tablas, los botones.

Esto se llama el patrón **MVC** (Modelo-Vista-Controlador) y por eso las carpetas se llaman así:

```
Proyecto1-304010957/
├── Models/              ← Las "recetas" (qué datos tiene cada cosa)
│   ├── Cliente.cs
│   ├── Empleado.cs
│   ├── Habitacion.cs
│   └── Reservacion.cs
│
├── Controllers/         ← Los "meseros" (qué hacer cuando alguien pide algo)
│   ├── ClientesController.cs
│   ├── EmpleadosController.cs
│   ├── HabitacionesController.cs
│   └── ReservacionesController.cs
│
├── Views/               ← La "decoración" (lo que la persona ve en pantalla)
│   ├── Clientes/
│   │   ├── Index.cshtml      ← Lista de todos los clientes
│   │   ├── Create.cshtml     ← Formulario para agregar uno nuevo
│   │   ├── Edit.cshtml       ← Formulario para cambiar datos
│   │   ├── Delete.cshtml     ← Pantalla para confirmar que se quiere borrar
│   │   └── Buscar.cshtml     ← Pantalla para buscar un cliente específico
│   ├── Empleados/        ← (misma estructura)
│   ├── Habitaciones/     ← (misma estructura)
│   └── Reservaciones/    ← (misma estructura)
│
├── Data/                ← La "memoria" del sistema
│   ├── DataStore.cs
│   └── UbicacionData.cs
│
└── Program.cs           ← El "interruptor" que enciende todo
```

Cada módulo (Clientes, Empleados, Habitaciones, Reservaciones) tiene sus propias carpetas en Views y su propio controlador. Esto permite que si hay que cambiar algo de Clientes, solo se toca esa parte sin afectar las demás, como si cada equipo de la pizzería trabajara en su propia estación.

---

## ¿Cómo funciona la memoria del sistema?

### El almacén de datos (DataStore.cs)

Imaginemos que tenemos cuatro cajas vacías en una mesa. Una caja es para guardar tarjetas de clientes, otra para tarjetas de empleados, otra para habitaciones y otra para reservaciones. Cada vez que agregamos un cliente, ponemos una tarjeta nueva en la caja de clientes.

Eso es exactamente lo que hace `DataStore.cs`:

```csharp
public static class DataStore
{
    public static List<Cliente> Clientes { get; set; } = new List<Cliente>();
    public static List<Empleado> Empleados { get; set; } = new List<Empleado>();
    public static List<Habitacion> Habitaciones { get; set; } = new List<Habitacion>();
    public static List<Reservacion> Reservaciones { get; set; } = new List<Reservacion>();
}
```

- `static` significa que estas cajas existen **una sola vez** para todo el programa. No hay copias. Todos los meseros (controladores) van a la misma mesa a buscar las mismas cajas.
- `List<Cliente>` es como decir "una caja que solo acepta tarjetas de clientes". No se puede meter una tarjeta de habitación ahí.
- `new List<Cliente>()` significa que al encender el programa, la caja empieza vacía.

Cuando el programa se apaga, es como si alguien tirara todas las cajas al piso: se pierde todo. Por eso los datos solo existen en memoria.

---

### Los modelos (las tarjetas)

Cada modelo define qué información va escrita en cada tarjeta. Por ejemplo, la tarjeta de un cliente:

```csharp
public class Cliente
{
    public string TipoIdentificacion { get; set; }  // ¿Es cédula, DIMEX o pasaporte?
    public string Identificacion { get; set; }       // El número
    public string Nombre { get; set; }               // El nombre
    public string PrimerApellido { get; set; }        // Primer apellido
    public string SegundoApellido { get; set; }       // Segundo apellido
    public DateTime FechaNacimiento { get; set; }     // Cuándo nació
}
```

Cada línea con `{ get; set; }` es como un espacio en blanco en la tarjeta para escribir un dato. `get` significa "puedo leer lo que dice" y `set` significa "puedo escribir algo nuevo".

Las validaciones (las reglas con `[Required]`, `[StringLength]`, `[Range]`) son como las instrucciones que dicen "este espacio NO puede quedar vacío" o "este número tiene que estar entre 1 y 500". Si alguien intenta dejar un espacio vacío, el sistema dice "¡no se puede!" y muestra un mensaje en rojo.

---

### Los controladores (los meseros)

Los controladores son los que hacen el trabajo pesado. Veamos cómo funciona agregar un nuevo cliente paso a paso:

```csharp
public IActionResult Create()
{
    return View();
}
```

Cuando alguien hace clic en "Nuevo Cliente", este método muestra el formulario vacío. Es como si el mesero le diera un papel en blanco al cliente para que llene sus datos.

```csharp
[HttpPost]
public IActionResult Create(Cliente cliente)
{
    if (DataStore.Clientes.Any(c => c.Identificacion == cliente.Identificacion))
    {
        ModelState.AddModelError("Identificacion", "Ya existe un cliente con esta identificación.");
    }

    if (ModelState.IsValid)
    {
        DataStore.Clientes.Add(cliente);
        return RedirectToAction(nameof(Index));
    }
    return View(cliente);
}
```

Cuando la persona llena el formulario y hace clic en "Guardar":

1. `[HttpPost]` significa "esto solo funciona cuando envían datos" (cuando presionan el botón de guardar).
2. Primero revisa si ya hay alguien con esa cédula: `DataStore.Clientes.Any(c => c.Identificacion == cliente.Identificacion)`. Es como buscar en la caja si ya existe una tarjeta con ese número. `.Any()` significa "¿hay alguna?".
3. Si todo está bien (`ModelState.IsValid`), mete la tarjeta nueva en la caja: `DataStore.Clientes.Add(cliente)`.
4. Después manda a la persona de vuelta a la lista de todos los clientes: `RedirectToAction(nameof(Index))`.
5. Si algo estaba mal, le devuelve el formulario con los errores marcados en rojo.

---

### Buscar (el método especial)

La búsqueda funciona separada del Index, como pide el proyecto. Es como tener un detective que busca en la caja una tarjeta específica:

```csharp
[HttpPost]
public IActionResult Buscar(string identificacion)
{
    var cliente = DataStore.Clientes.FirstOrDefault(c => c.Identificacion == identificacion);
    if (cliente == null)
    {
        ViewBag.Mensaje = "No se encontró un cliente con esa identificación.";
        return View();
    }
    return View(cliente);
}
```

- `FirstOrDefault` busca en la caja la primera tarjeta que tenga esa identificación. Si no la encuentra, devuelve `null` (nada).
- Si no encuentra nada, muestra un mensaje de "no se encontró".
- Si la encuentra, muestra los datos de esa tarjeta.

---

### La regla de protección (integridad)

El sistema tiene una regla importante: no se puede borrar un cliente o una habitación si tiene una reservación. Es como decir "no puedes tirar la tarjeta de un huésped si todavía tiene una habitación reservada".

```csharp
[HttpPost, ActionName("Delete")]
public IActionResult DeleteConfirmed(string id)
{
    var cliente = DataStore.Clientes.FirstOrDefault(c => c.Identificacion == id);
    if (cliente == null) return NotFound();

    bool tieneReservaciones = DataStore.Reservaciones.Any(r => r.IdentificacionCliente == id);
    if (tieneReservaciones)
    {
        TempData["Error"] = "No se puede eliminar el cliente porque tiene reservaciones asociadas.";
        return View(cliente);
    }

    DataStore.Clientes.Remove(cliente);
    return RedirectToAction(nameof(Index));
}
```

Antes de borrar, revisa en la caja de reservaciones si hay alguna tarjeta que mencione a ese cliente. Si la hay, no lo borra y muestra un mensaje de error. Lo mismo pasa con las habitaciones.

---

### Validación de reservaciones (sin traslapes)

Cuando se crea una reservación, el sistema revisa que la habitación no esté ocupada en esas fechas:

```csharp
var reservacionesHabitacion = DataStore.Reservaciones
    .Where(r => r.NumeroHabitacion == reservacion.NumeroHabitacion
        && r.Estado != "Cancelada"
        && r.Estado != "Completada");

foreach (var r in reservacionesHabitacion)
{
    if (reservacion.FechaIngreso < r.FechaSalida && reservacion.FechaSalida > r.FechaIngreso)
    {
        ModelState.AddModelError("NumeroHabitacion",
            $"La habitación ya tiene una reservación en esas fechas.");
        break;
    }
}
```

Esto busca todas las reservaciones de esa habitación que no estén canceladas ni completadas. Luego revisa si las fechas se cruzan. Si la nueva reservación empieza antes de que termine otra y termina después de que empiece la otra, entonces hay un traslape y no se permite.

---

### Los combos anidados de ubicación (Provincia > Cantón > Distrito)

Para los empleados, cuando se selecciona una provincia, se cargan los cantones de esa provincia, y cuando se selecciona un cantón, se cargan los distritos. Esto funciona con JavaScript y llamadas al servidor:

```javascript
$('#ddlProvincia').change(function () {
    var provincia = $(this).val();
    $('#ddlCanton').empty().append('<option value="">-- Seleccione --</option>');
    $('#ddlDistrito').empty().append('<option value="">-- Seleccione --</option>');
    if (provincia) {
        $.getJSON('/Empleados/GetCantones', { provincia: provincia }, function (cantones) {
            $.each(cantones, function (i, canton) {
                $('#ddlCanton').append('<option value="' + canton + '">' + canton + '</option>');
            });
        });
    }
});
```

Cuando alguien escoge una provincia, este código le pregunta al servidor "¿cuáles son los cantones de esta provincia?" y el servidor responde con la lista. Luego pone esas opciones en la lista desplegable de cantones. Lo mismo pasa cuando se escoge un cantón para cargar los distritos.

El servidor responde desde el controlador de empleados:

```csharp
public JsonResult GetCantones(string provincia)
{
    var cantones = UbicacionData.GetCantones(provincia);
    return Json(cantones);
}
```

Y los datos están guardados en `UbicacionData.cs`, que tiene todas las 7 provincias de Costa Rica con sus cantones y distritos cargados directamente en el código.

---

### Program.cs (el interruptor)

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllersWithViews();
var app = builder.Build();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```

Este archivo es el que enciende todo. `AddControllersWithViews()` le dice al programa "vamos a usar el patrón MVC". La ruta `{controller=Home}/{action=Index}/{id?}` define cómo se leen las direcciones web: si alguien va a `/Clientes/Create`, el sistema sabe que debe ir al controlador `Clientes` y ejecutar la acción `Create`. Si no se escribe nada, va a `Home/Index` por defecto.

---

## Demostración paso a paso

1. **Inicio**: Al abrir la aplicación, se ve la página principal con cuatro tarjetas de colores para cada sección.

2. **Agregar un cliente**: Hacer clic en "Ir a Clientes" > "Nuevo Cliente" > Llenar el formulario > "Guardar". El cliente aparece en la tabla.

3. **Buscar un cliente**: Desde la lista de clientes, hacer clic en "Buscar Cliente" > Escribir la identificación > "Buscar". Muestra los datos del cliente encontrado.

4. **Editar un cliente**: En la tabla de clientes, hacer clic en "Editar" junto al cliente deseado > Cambiar los datos > "Guardar Cambios".

5. **Agregar una habitación**: Ir a Habitaciones > "Nueva Habitación" > Llenar número, tipo, tarifa, TV satelital > "Guardar".

6. **Crear una reservación**: Primero debe haber al menos un cliente y una habitación registrados. Ir a Reservaciones > "Nueva Reservación" > Seleccionar el cliente y la habitación de las listas > Poner fechas y cantidad de personas > "Guardar".

7. **Intentar borrar un cliente con reservación**: Ir a Clientes > "Eliminar" junto al cliente que tiene reservación > "Confirmar Eliminación". El sistema muestra un error indicando que no se puede eliminar porque tiene reservaciones asociadas.

8. **Empleados con ubicación**: Ir a Empleados > "Nuevo Empleado" > Al seleccionar la provincia, se cargan automáticamente los cantones, y al seleccionar el cantón, se cargan los distritos.
