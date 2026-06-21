Temas de Estudio

1. Tema 1. Fundamentos de diseño y programación Web.

2. Programación orientada a objetos en C#, patrón MVC y uso de Razor para la construcción de
vistas. Manejo de datos en memoria mediante clases y colecciones de objetos.

Objetivo

Desarrollar un software web utilizando C# y el patrón de arquitectura MVC (Modelo-Vista-Controlador),
que implemente operaciones CRUD (Crear, Leer, Actualizar y Eliminar) aplicadas a un caso real, con el
fin de fortalecer las competencias en diseño estructurado de aplicaciones, manejo de aplicaciones web,
y buenas prácticas de programación orientada a objetos (POO).

Herramientas y Tecnologías Obligatorias

• IDE oficial: Visual Studio Community 2026  
• Lenguaje de programación: C#  
• Framework: .NET 10.0  
• Paradigma: Programación Orientada a Objetos (POO)  
• Arquitectura: MVC (Modelo-Vista-Controlador)

Fundamentos de Programación web Código: 03075  
II Cuatrimestre 2026 Página 2 de 10

• Los datos serán almacenados en memoria mediante variables de aplicación, es decir, no se utilizarán
bases de datos.

• Se recomienda el uso de caché, no habrá persistencia, cuando la aplicación se cierra, los datos se
pierden.

Se debe crear clases y colecciones de objetos para representar la información del sistema.

hospedaje en la provincia de Guanacaste. Su tarea consiste en desarrollar la primera etapa de un
sistema web que permita gestionar la información básica de clientes, habitaciones, empleados y
reservaciones del negocio.

Consideraciones de Arquitectura y Datos

• Los datos serán almacenados temporalmente en memoria mediante colecciones de objetos,
asociadas a las clases del modelo. Es decir, cuando la aplicación se detiene o se cierra, los
datos se pierden. No se permite el uso de bases de datos, archivos externos, Entity
Framework ni servicios externos para la persistencia de datos en este primer proyecto.

• Se deben implementar clases bien definidas y colecciones de objetos para representar la
información del sistema.

Requerimientos Funcionales

El sistema debe permitir la gestión completa (operaciones CRUD: Crear, Leer, Actualizar y
Eliminar).

En esta fase, el sistema debe permitir:

• Registro y administración de clientes  
• Registro y administración de empleados  
• Registro y administración de habitaciones  
• Registro y administración de reservaciones

### Gestión de Clientes

Campos obligatorios:

• Identificación (una de las siguientes):

o Cédula: 11 dígitos con guiones (ej. 1-1111-0909).  
o DIMEX: 12 dígitos numéricos.  
o Pasaporte: Alfanumérico (de 1 a 50 caracteres).

• Nombre: Alfanumérico (de 3 a 50 caracteres).  
• Primer Apellido: Alfanumérico (de 3 a 75 caracteres).  
• Segundo Apellido: Alfanumérico (de 3 a 75 caracteres).  
• Fecha de Nacimiento: Formato DD/MM/AAAA (ej. 15/08/1984)

### Gestión de Empleados

Campos obligatorios:

• Identificación: Cédula, DIMEX (bajo los mismos formatos de clientes).  
• Nombre y Apellidos: Igual que clientes.  
• Fecha de Nacimiento: Formato DD/MM/AAAA.  
• Salario Mensual: Valor numérico en el rango de 0 a 5,000,000.  
• Fecha de Ingreso: Formato DD/MM/AAAA.  
• Categoría: Selección entre: mesero, salonero, lavaplatos, recepcionista, administrador,
mantenimiento, cocinero, Chef, limpieza, cocina, seguridad, atención al cliente, guía turístico
y encargado de reservaciones.  
• Ubicación: Selección de Provincia, Cantón y Distrito mediante listas desplegables (combos
anidados según la división territorial de Costa Rica). Estos datos se pueden cargar
directamente en memoria cada vez que inicie el programa.  
• Dirección Exacta: Alfanumérico (de 1 a 150 caracteres).

### Gestión de Habitaciones

Campos obligatorios:

• Número de Habitación: Numérico en el rango de 1 a 500.  
• Tipo de Habitación: Selección entre: Start Junior, Start Vista al Mar, Master Start  
• Tarifa por Noche: Numérico en el rango de 50 a 800 dólares.  
• TV Satelital: (Sí / No).  
• Pendientes de Mantenimiento: Texto alfanumérico (de 0 a 500 caracteres).

### Gestión de Reservaciones

Campos obligatorios:

• Código de Reservación: Alfanumérico (de 1 a 20 caracteres).  
• Identificación del Cliente: Debe corresponder obligatoriamente a un cliente ya registrado
en el sistema.  
• Número de Habitación: Debe corresponder obligatoriamente a una habitación ya registrada
en el sistema. Además, el sistema debe validar que la habitación esté disponible para el
rango de fechas seleccionado, evitando reservaciones traslapadas en memoria.  
• Fecha de Ingreso: Formato DD/MM/AAAA.  
• Fecha de Salida: Formato DD/MM/AAAA (debe validarse que sea posterior a la fecha de
ingreso).  
• Cantidad de Personas: Numérico en el rango de 1 a 10.  
• Estado de la Reservación: selección entre: Reservada, Confirmada, Cancelada,
Completada.

Regla de integridad:

No se debe permitir eliminar clientes ni habitaciones que estén asociados a una
reservación registrada. En caso de intentarlo, el sistema debe mostrar un mensaje claro en la interfaz
indicando la razón.

### Requerimientos de Interfaz y Navegación

• Menú de Navegación: El sitio web debe contar con un menú para moverse fluidamente
entre las secciones de Clientes, Empleados, Habitaciones y Reservaciones.

• Módulo de búsqueda: Las operaciones de buscar para clientes, empleados, habitaciones y
reservaciones no se deben realizar dentro de la vista Index. Se debe implementar un
método específico para cada caso.

Criterios mínimos de búsqueda:

clientes por identificación, empleados por identificación, habitaciones por número de habitación y
reservaciones por código de reservación o identificación del cliente.

### Rúbrica de Evaluación

**Inserción de empleados**  
Registra empleados correctamente desde la interfaz web y se evidencia una conexión adecuada con el controlador.

**Búsqueda de empleados**  
Implementa un método de búsqueda propio, funcional y separado del index.

**Eliminación de empleados**  
Elimina empleados correctamente desde la interfaz web y refleja el cambio en el sistema.

**Actualización de empleados**  
Modifica los datos de empleados correctamente desde la interfaz web y guarda los cambios mediante el controlador.

**Inserción de clientes**  
Registra clientes correctamente desde la interfaz web y se evidencia una conexión adecuada con el controlador.

**Búsqueda de clientes**  
Implementa un método de búsqueda propio, funcional y separado del index.

**Eliminación de clientes**  
Elimina clientes correctamente desde la interfaz web y refleja el cambio en el sistema.

**Actualización de clientes**  
Modifica los datos de clientes correctamente desde la interfaz web y guarda los cambios mediante el controlador.

**Inserción de habitaciones**  
Registra habitaciones correctamente desde la interfaz web y se evidencia una conexión adecuada con el controlador.

**Búsqueda de habitaciones**  
Implementa un método de búsqueda propio, funcional y separado del index.

**Eliminación de habitaciones**  
Elimina habitaciones correctamente desde la interfaz web y refleja el cambio en el sistema.

**Actualización de habitaciones**  
Modifica los datos de habitaciones correctamente desde la interfaz web y guarda los cambios mediante el controlador.

**Inserción de reservaciones**  
Registra reservaciones correctamente desde la interfaz web y se evidencia una conexión adecuada con el controlador.

**Búsqueda de reservaciones**  
Implementa un método de búsqueda propio, funcional y separado del index. Permite consultar la información de forma clara.

**Eliminación de reservaciones**  
Elimina reservaciones correctamente desde la interfaz web y refleja el cambio en el sistema.

**Actualización de reservaciones**  
Modifica los datos de reservaciones correctamente desde la interfaz web y guarda los cambios mediante el controlador.
