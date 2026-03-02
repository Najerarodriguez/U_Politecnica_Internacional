```
/sistema
│
├── domain/
│   ├── user.py
│
└── main.py
```

---

### domain/user.py

```python
class User:
    def __init__(self, username, password):
        self._username = username
        self._password = password
        self._logged = False

    def login(self, password):
        self._logged = self._password == password
        return self._logged

    @property
    def is_logged(self):
        return self._logged
```

---

### main.py

```python
# Primera ejecución del sistema usando exactamente la clase original

from domain.user import User

u = User("admin", "123")

print(u.is_logged)   # False

u.login("123")

print(u.is_logged)   # True
```


# SEGUNDA OPCION  **********************************************************************************************************
```text
/sistema
│
├── domain/
│   ├── user.py
│   ├── product.py
│
└── main.py
```

---

### domain/user.py

```python
class User:
    def __init__(self, username, password):
        self._username = username
        self._password = password
        self._logged = False

    def login(self, password):
        self._logged = self._password == password
        return self._logged

    @property
    def is_logged(self):
        return self._logged
```

---

### domain/product.py

```python
# Segunda parte: Herencia y polimorfismo (Open/Closed Principle)

class Product:
    def __init__(self, name, price):
        self._name = name
        self._price = price

    @property
    def price(self):
        return self._price


class Milk(Product):
    def __init__(self):
        super().__init__("Leche", 1000)


class Rice(Product):
    def __init__(self):
        super().__init__("Arroz", 2500)
```

---

### main.py

```python
from domain.user import User
from domain.product import Milk, Rice

u = User("admin", "123")
u.login("123")

milk = Milk()
rice = Rice()

print(milk.price)   # 1000
print(rice.price)   # 2500
```

#Tercera parte  **********************************************************************************************************

```text id="w1kqz9"
/sistema
│
├── domain/
│   ├── user.py
│   ├── product.py
│   ├── state.py
│
└── main.py
```

---

### domain/user.py

```python id="u1p3x8"
class User:
    def __init__(self, username, password):
        self._username = username
        self._password = password
        self._logged = False

    def login(self, password):
        self._logged = self._password == password
        return self._logged

    @property
    def is_logged(self):
        return self._logged
```

---

### domain/product.py

```python id="p9z4m2"
class Product:
    def __init__(self, name, price):
        self._name = name
        self._price = price

    @property
    def price(self):
        return self._price


class Milk(Product):
    def __init__(self):
        super().__init__("Leche", 1000)


class Rice(Product):
    def __init__(self):
        super().__init__("Arroz", 2500)
```

---

### domain/state.py

```python id="s7n2k5"
# Tercera parte: Patrón State
# Se modelan estados que cambian con el tiempo
# Las transiciones están controladas dentro de cada estado

class State:
    def next(self, order):
        pass


class Created(State):
    def next(self, order):
        order._state = Paid()


class Paid(State):
    def next(self, order):
        order._state = Delivered()


class Delivered(State):
    def next(self, order):
        pass
```

---

### main.py

```python id="m3v8q1"
# En esta etapa solo verificamos que los estados puedan instanciarse

from domain.state import Created, Paid, Delivered

s1 = Created()
s2 = Paid()
s3 = Delivered()

print(type(s1).__name__)  # Created
print(type(s2).__name__)  # Paid
print(type(s3).__name__)  # Delivered
```


# Cuarta parte  **********************************************************************************************************
```text
/sistema
│
├── domain/
│   ├── user.py
│   ├── product.py
│   ├── state.py
│   ├── order.py
│
└── main.py
```

---

### domain/user.py

```python
class User:
    def __init__(self, username, password):
        self._username = username
        self._password = password
        self._logged = False

    def login(self, password):
        self._logged = self._password == password
        return self._logged

    @property
    def is_logged(self):
        return self._logged
```

---

### domain/product.py

```python
class Product:
    def __init__(self, name, price):
        self._name = name
        self._price = price

    @property
    def price(self):
        return self._price


class Milk(Product):
    def __init__(self):
        super().__init__("Leche", 1000)


class Rice(Product):
    def __init__(self):
        super().__init__("Arroz", 2500)
```

---

### domain/state.py

```python
class State:
    def next(self, order):
        pass


class Created(State):
    def next(self, order):
        order._state = Paid()


class Paid(State):
    def next(self, order):
        order._state = Delivered()


class Delivered(State):
    def next(self, order):
        pass
```

---

### domain/order.py

```python
# Cuarta parte: Encapsulación de reglas de negocio + coherencia del modelo
# El pedido controla:
# - Que el usuario esté autenticado
# - Que solo se pueda modificar en estado Created
# - El cálculo del total

from .state import Created

class Order:
    def __init__(self, user):
        if not user.is_logged:
            raise Exception("Usuario no autenticado")
        self._user = user
        self._products = []
        self._state = Created()

    def add_product(self, product):
        if type(self._state).__name__ != "Created":
            raise Exception("No se puede modificar el pedido")
        self._products.append(product)

    def total(self):
        return sum(p.price for p in self._products)

    def next_state(self):
        self._state.next(self)
```

---

### main.py

```python
from domain.user import User
from domain.product import Milk, Rice
from domain.order import Order

u = User("admin", "123")
u.login("123")

order = Order(u)

order.add_product(Milk())
order.add_product(Rice())

print(order.total())  # 3500

order.next_state()    # Transición Created -> Paid
```
