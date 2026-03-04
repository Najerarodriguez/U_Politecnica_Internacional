```python
"""
Estructura sugerida:

/sistema
│
├── domain/
│   ├── user.py
│   ├── product.py
│   ├── state.py
│   ├── order.py
│
└── main.py
"""

# =========================
# domain/user.py
# =========================

class User:
    def __init__(self, username: str, password: str):
        # OBS: Guardar password en texto plano en memoria es inseguro en un sistema real.
        #      En producción, usar hashing (bcrypt/argon2) y mover autenticación a un servicio (AuthService),
        #      evitando que la entidad User gestione sesión directamente.
        self._username = username
        self._password = password
        self._logged = False  # OBS: "estado de sesión" en la entidad suele ser mala separación de responsabilidades.

    def login(self, password: str) -> bool:
        # OBS: Comparación directa de strings para password es solo didáctica; en real usar hash + verificación constante.
        self._logged = self._password == password
        return self._logged

    @property
    def is_logged(self) -> bool:
        return self._logged


# =========================
# domain/product.py
# =========================

class Product:
    def __init__(self, name: str, price: int):
        # OBS: Falta validar invariantes: price debe ser numérico, >= 0.
        #      Si se usa Decimal para dinero, evitar errores de precisión.
        self._name = name
        self._price = price

    @property
    def price(self) -> int:
        return self._price


class Milk(Product):
    def __init__(self):
        # OBS: Herencia aquí es sobre-modelado: solo fija constantes.
        #      Cada nuevo producto => nueva clase (boilerplate y rigidez).
        #      Alternativa mejor: Product("Leche", 1000) o catálogo/fábrica basado en datos (DB/JSON).
        super().__init__("Leche", 1000)


class Rice(Product):
    def __init__(self):
        # OBS: Mismo problema: herencia sin comportamiento adicional.
        super().__init__("Arroz", 2500)


# =========================
# domain/state.py
# =========================

class State:
    def next(self, order):
        # OBS: Contrato no definido: ¿retorna nuevo estado o muta order?
        #      Es preferible que retorne el nuevo estado y que Order sea el único que asigna self._state.
        raise NotImplementedError

    def can_modify(self) -> bool:
        # OBS: Mejor que Order pregunte al estado (polimorfismo) en vez de comparar strings de nombres de clase.
        return False


class Created(State):
    def next(self, order):
        # OBS: El diseño original hacía: order._state = Paid()
        #      Eso VIOLA encapsulación: un externo muta un atributo "privado" de Order.
        #      Alternativa mejor: retornar Paid() y que Order asigne.
        return Paid()

    def can_modify(self) -> bool:
        return True


class Paid(State):
    def next(self, order):
        return Delivered()


class Delivered(State):
    def next(self, order):
        # OBS: El original hacía "pass" silencioso; eso es peligroso porque aparenta éxito pero no cambia nada.
        #      En un estado terminal debe fallar explícitamente.
        raise RuntimeError("Transición inválida: Delivered es un estado terminal")


# =========================
# domain/order.py
# =========================

# OBS: Order como agregado raíz es una buena decisión: centraliza invariantes y reglas (cohesión alta).
# OBS: Evitar raise Exception genérico; usar excepciones semánticas (PermissionError, ValueError, etc.)
#      o excepciones de dominio.

class Order:
    def __init__(self, user: User):
        # OBS: Validar autenticación aquí es coherente con la regla de negocio del agregado,
        #      pero en sistemas reales esto suele depender de un contexto de sesión/token externo.
        if not user.is_logged:
            # OBS: Mejor: raise PermissionError(...) o UnauthenticatedUser(...)
            raise PermissionError("Usuario no autenticado")

        self._user = user
        self._products: list[Product] = []
        self._state: State = Created()

    def _set_state(self, new_state: State) -> None:
        # OBS: Punto único de mutación del estado en Order para mantener encapsulación e invariantes.
        self._state = new_state

    def add_product(self, product: Product) -> None:
        # OBS: El original comparaba: type(self._state).__name__ != "Created" (fragilidad por strings).
        #      Mejor: polimorfismo (can_modify) o isinstance(self._state, Created).
        if not self._state.can_modify():
            # OBS: Mejor: OrderNotModifiable(...) / InvalidStateTransition(...)
            raise RuntimeError("No se puede modificar el pedido en el estado actual")

        # OBS: Falta validar invariantes del producto (precio no negativo, tipo correcto, etc.).
        self._products.append(product)

    def total(self) -> int:
        # OBS: Para dinero, preferible Decimal y validaciones de tipo.
        return sum(p.price for p in self._products)

    def next_state(self) -> None:
        # OBS: Delegación a State (patrón State) es correcta si hay reglas por estado.
        #      Si solo hay 3 estados lineales con pocas reglas, una enum + tabla de transición puede ser más simple.
        new_state = self._state.next(self)  # OBS: Estado retorna el siguiente estado (no muta order).
        self._set_state(new_state)


# =========================
# main.py
# =========================

if __name__ == "__main__":
    u = User("admin", "123")

    # OBS: login con password plano es didáctico; en real usar servicio de autenticación.
    u.login("123")

    order = Order(u)

    # OBS: Evitar subclases Milk/Rice; mejor instanciar Product parametrizado o usar catálogo.
    order.add_product(Milk())
    order.add_product(Rice())

    print(order.total())  # 3500

    order.next_state()    # Transición Created -> Paid
    # order.next_state()  # Paid -> Delivered
    # order.next_state()  # RuntimeError: Delivered es terminal
```
