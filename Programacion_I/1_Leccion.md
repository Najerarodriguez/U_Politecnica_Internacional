### 📌 Sistema operativo

```bash
uname -a
```
---

### 📌 Arquitectura del procesador (CPU)

```bash
uname -m
```

---

### 📌 Memoria RAM

```bash
free -h
```

---
Solo el directorio actual:

```bash
pwd
```

---

### 📌 Verificar si el compilador de C está instalado (gcc)

```bash
gcc --version
```

Si **NO** está instalado, instálalo con:

```bash
sudo apt update
sudo apt install gcc -y
```

---

## 2️⃣ Crear el programa en C (Hola Mundo)

### 📌 Crear el archivo

```bash
nano hola.c
```

### 📌 Código en C

```c
#include <stdio.h>

int main(void) {
    printf("Hola mundo\n");
    return 0;
}
```

Guarda con:

```
CTRL + O  → Enter
CTRL + X
```

---

## 3️⃣ Compilar el programa

```bash
gcc hola.c -o hola
```

Si **no aparece ningún error**, la compilación fue exitosa.

---

## 4️⃣ Ejecutar el programa

```bash
./hola
```

Salida esperada:

```
Hola mundo
```

---

## 5️⃣ Verificación rápida (opcional)

```bash
file hola
```

Confirma que es un ejecutable para Linux según la arquitectura.

---

## 1️⃣ Explicación detallada del código *Hola Mundo*

```c
#include <stdio.h>
```

**Por qué es necesario**

* `#include` le indica al preprocesador que **incluya una biblioteca estándar** antes de compilar.
* `stdio.h` contiene las declaraciones de funciones de **entrada y salida estándar**, como `printf()` y `scanf()`.
* Sin esta línea, el compilador **no sabría qué es `printf`** y produciría un error.

---

```c
int main(void) {
```

**Por qué es necesario**

* `main` es la **función principal** del programa en C.
* Es el **punto de entrada**: el sistema operativo empieza a ejecutar el programa desde aquí.
* `int` indica que la función devuelve un **valor entero** al sistema operativo.
* `void` indica que `main` **no recibe argumentos**.

---

```c
printf("Hola mundo\n");
```

**Por qué es necesario**

* `printf` imprime texto en la **salida estándar** (pantalla).
* `"Hola mundo"` es el mensaje a mostrar.
* `\n` es un **salto de línea**, mejora la presentación del texto.

---

```c
return 0;
```

**Por qué es necesario**

* Devuelve `0` al sistema operativo.
* Por convención, `0` indica que el programa **finalizó correctamente**.
* Es una buena práctica en programación profesional.

---

```c
}
```

**Por qué es necesario**

* Indica el **fin del bloque** de la función `main`.

---

---

# CODIFICACIÓN EN C — EJERCICIOS

Cada ejercicio es **independiente**, con comentarios claros.

---

## 2️⃣ Solicitar un texto y mostrarlo

### 📌 Requisito

> Mostrar:
> **"El texto es el siguiente: '{texto}'"**

```c
#include <stdio.h>

int main(void) {

    // Declaramos un arreglo de caracteres para almacenar el texto ingresado
    char texto[100];

    // Solicitamos al usuario que ingrese un texto
    printf("Ingrese un texto: ");

    // Leemos una línea completa de texto desde el teclado
    // fgets es más seguro que scanf para cadenas
    fgets(texto, sizeof(texto), stdin);

    // Mostramos el texto ingresado por el usuario
    printf("El texto es el siguiente: '%s'\n", texto);

    return 0;
}
```

---

## 3️⃣ Solicitar dos números enteros y mostrarlos

### 📌 Requisito

> Mostrar:
> **"Los numeros son {numero} y {numero}"**

```c
#include <stdio.h>

int main(void) {

    // Declaramos dos variables enteras
    int num1, num2;

    // Solicitamos el primer número
    printf("Ingrese el primer numero entero: ");
    scanf("%d", &num1);

    // Solicitamos el segundo número
    printf("Ingrese el segundo numero entero: ");
    scanf("%d", &num2);

    // Mostramos los números ingresados
    printf("Los numeros son %d y %d\n", num1, num2);

    return 0;
}
```

---

## 4️⃣ Solicitar dos números, sumarlos y mostrar el resultado

### 📌 Requisito

> Mostrar:
> **"La suma de {numero} + {numero} es {resultado}"**

```c
#include <stdio.h>

int main(void) {

    // Declaramos variables para los números y el resultado
    int num1, num2, suma;

    // Solicitamos el primer número
    printf("Ingrese el primer numero: ");
    scanf("%d", &num1);

    // Solicitamos el segundo número
    printf("Ingrese el segundo numero: ");
    scanf("%d", &num2);

    // Realizamos la suma
    suma = num1 + num2;

    // Mostramos el resultado de la operación
    printf("La suma de %d + %d es %d\n", num1, num2, suma);

    return 0;
}
```

---

# 1️⃣ Crear la estructura de archivos (en la terminal)

```bash
touch main.c suma.c resta.c multiplicacion.c division.c
```

Verificar que existen:

```bash
ls
```

Debes ver:

```
main.c  suma.c  resta.c  multiplicacion.c  division.c
```

---

# 2️⃣ Archivo de SUMA (archivo independiente)

## 📄 `suma.c`

```c
// Funcion que realiza la suma de dos numeros enteros
int sumar(int a, int b) {
    return a + b;
}
```

---

# 3️⃣ Archivo de RESTA (archivo independiente)

## 📄 `resta.c`

```c
// Funcion que realiza la resta de dos numeros enteros
int restar(int a, int b) {
    return a - b;
}
```

---

# 4️⃣ Archivo de MULTIPLICACIÓN (archivo independiente)

## 📄 `multiplicacion.c`

```c
// Funcion que realiza la multiplicacion de dos numeros enteros
int multiplicar(int a, int b) {
    return a * b;
}
```

---

# 5️⃣ Archivo de DIVISIÓN (archivo independiente)

## 📄 `division.c`

```c
// Funcion que realiza la division de dos numeros enteros
// Se asume que el divisor es distinto de cero
float dividir(int a, int b) {
    return (float)a / b;
}
```

---

# 6️⃣ Archivo PRINCIPAL que usa los otros archivos

## 📄 `main.c`

```c
#include <stdio.h>

/*
 Declaracion de las funciones.
 El compilador necesita conocer sus firmas
 antes de que sean usadas.
*/
int sumar(int a, int b);
int restar(int a, int b);
int multiplicar(int a, int b);
float dividir(int a, int b);

int main(void) {

    int num1, num2;

    printf("Ingrese el primer numero: ");
    scanf("%d", &num1);

    printf("Ingrese el segundo numero: ");
    scanf("%d", &num2);

    printf("Suma: %d\n", sumar(num1, num2));
    printf("Resta: %d\n", restar(num1, num2));
    printf("Multiplicacion: %d\n", multiplicar(num1, num2));

    if (num2 != 0) {
        printf("Division: %.2f\n", dividir(num1, num2));
    } else {
        printf("Division: No se puede dividir entre cero\n");
    }

    return 0;
}
```

---

# 7️⃣ Compilar TODOS los archivos (enlace correcto)

```bash
gcc main.c suma.c resta.c multiplicacion.c division.c -o calculadora
```

Ejecutar:

```bash
./calculadora
```


---

### 📁 Estructura de archivos 5 archivos en Python**, con **un `main.py`** y **cuatro módulos**, cada uno con **una sola operación**

```
calculadora/
│
├── main.py
├── suma.py
├── resta.py
├── multiplicacion.py
└── division.py
```

---

### 📄 `suma.py`

```python
def sumar(a, b):
    return a + b
```

---

### 📄 `resta.py`

```python
def restar(a, b):
    return a - b
```

---

### 📄 `multiplicacion.py`

```python
def multiplicar(a, b):
    return a * b
```

---

### 📄 `division.py`

```python
def dividir(a, b):
    if b == 0:
        return "Error: división entre cero"
    return a / b
```

---

### 📄 `main.py`

```python
from suma import sumar
from resta import restar
from multiplicacion import multiplicar
from division import dividir

def main():
    a = float(input("Ingrese el primer número: "))
    b = float(input("Ingrese el segundo número: "))

    print("Suma:", sumar(a, b))
    print("Resta:", restar(a, b))
    print("Multiplicación:", multiplicar(a, b))
    print("División:", dividir(a, b))

if __name__ == "__main__":
    main()
```

---

### ▶️ Ejecución

Desde el directorio `calculadora`:

```bash
python main.py
```

---
```bash
python -m PyInstaller --onefile main.py
./dist/main
```
