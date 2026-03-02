Requerimientos del Sistema de Gestión de Pedidos – Tienda Minorista “AlfaMarket”

Contexto del negocio
AlfaMarket es una tienda minorista que vende productos básicos de consumo (ej. leche, arroz). Opera inicialmente con catálogo reducido (2 productos), precios fijos definidos por administración y ventas directas sin descuentos ni promociones. El sistema debe gestionar pedidos simples con un flujo lineal de estados y control mínimo de autenticación.

Datos de negocio (inventados)

* Usuarios activos iniciales: 5 administradores internos.
* Productos iniciales:

  * Leche entera 1L: $1.000
  * Arroz 1kg: $2.500
* Moneda: pesos locales.
* Un pedido puede contener múltiples productos sin límite de cantidad.
* No se maneja inventario ni impuestos en esta versión.

Requerimientos funcionales

1. Autenticación básica

   * El sistema debe permitir que un usuario se autentique mediante usuario y contraseña.
   * Solo usuarios autenticados pueden crear pedidos.
   * El sistema debe validar credenciales antes de permitir operaciones.

2. Creación de pedido

   * Un pedido debe asociarse obligatoriamente a un usuario autenticado.
   * Al crearse, el pedido debe iniciar en estado “Created”.

3. Gestión de productos en el pedido

   * Solo se pueden agregar productos mientras el pedido esté en estado “Created”.
   * Cada producto tiene nombre y precio unitario.
   * El pedido debe permitir múltiples productos.
   * El sistema debe calcular el total como suma de los precios de los productos agregados.

4. Flujo de estados del pedido

   * El pedido debe soportar los siguientes estados:

     * Created
     * Paid
     * Delivered
   * Transiciones válidas:

     * Created → Paid
     * Paid → Delivered
   * No se permiten transiciones fuera de este flujo.
   * Delivered es estado terminal y no permite más cambios ni transiciones.

5. Reglas de integridad

   * No se pueden modificar productos si el pedido no está en estado “Created”.
   * No se puede avanzar de estado si la transición no es válida.
   * El total debe calcularse dinámicamente según los productos actuales.

Requerimientos no funcionales

1. Diseño del dominio

   * Las reglas de negocio deben estar encapsuladas en el modelo de dominio.
   * El pedido debe actuar como raíz de agregado y controlar sus invariantes.
   * El flujo de estados debe implementarse mediante un patrón que permita extender reglas por estado sin modificar condicionales globales.

2. Extensibilidad

   * El sistema debe permitir agregar nuevos estados en el futuro sin reescribir la lógica central del pedido.
   * El catálogo de productos debe poder ampliarse sin modificar la lógica del pedido.

3. Mantenibilidad

   * El código debe evitar dependencias innecesarias entre módulos.
   * Las transiciones de estado deben ser explícitas y controladas.

Alcance de la versión actual
No se incluye persistencia en base de datos, manejo de inventario, facturación, descuentos, concurrencia ni auditoría. El sistema es una base conceptual para evolucionar hacia un motor de pedidos más robusto.
