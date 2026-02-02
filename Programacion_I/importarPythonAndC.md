```
gcloud config unset project
```


```c
/* main.c
   Archivo principal que muestra un menú al usuario y llama a la función
   definida en elevar.c para calcular una potencia.
*/

#include <stdio.h>

/* Prototipo de la función que está definida en elevar.c */
void elevar(void);

int main(void) {
    int opcion = 0;

    do {
        /* Mostrar el menú al usuario */
        printf("\n=== MENU ===\n");
        printf("1) Realizar elevar a la potencia un numero\n");
        printf("2) Salir del programa\n");
        printf("Seleccione una opcion: ");
        scanf("%d", &opcion);

        /* Evaluar la opción elegida */
        switch (opcion) {
            case 1:
                /* Llamar a la función que calcula la potencia */
                elevar();
                break;
            case 2:
                /* Salir del programa */
                printf("Saliendo del programa...\n");
                break;
            default:
                /* Opción no válida */
                printf("Opcion invalida. Intente de nuevo.\n");
                break;
        }
    } while (opcion != 2);

    return 0;
}
```

```c
/* elevar.c
   Este archivo contiene la función que solicita la base y el exponente
   y calcula la potencia multiplicando la base por sí misma tantas veces
   como indique el exponente.
*/

#include <stdio.h>

/* Definición de la función elevar */
void elevar(void) {
    int base = 0;
    int exponente = 0;
    int resultado = 1;
    int i = 0;

    /* Solicitar la base al usuario */
    printf("Ingrese la base: ");
    scanf("%d", &base);

    /* Solicitar el exponente al usuario */
    printf("Ingrese el exponente: ");
    scanf("%d", &exponente);

    /* Calcular la potencia usando multiplicaciones repetidas */
    for (i = 0; i < exponente; i++) {
        resultado = resultado * base;
    }

    /* Mostrar el resultado */
    printf("El resultado de %d elevado a %d es: %d\n", base, exponente, resultado);
}
```

```
gcc main.c elevar.c -o programa
```


Perfecto, lo hacemos en **dos archivos** y lo dejamos **compilable a un ejecutable**.

### 📄 elevar.py

```python
def elevar():
    b=int(input("Ingrese la base: "))
    e=int(input("Ingrese el exponente: "))
    r=1
    for _ in range(e): r*=b
    print(f"El resultado de {b} elevado a {e} es: {r}")
```

### 📄 main.py

```python
from elevar import elevar

while True:
    print("\n=== MENU ===\n1) Realizar elevar a la potencia un numero\n2) Salir del programa")
    o=int(input("Seleccione una opcion: "))
    if o==1: elevar()
    elif o==2: break
    else: print("Opcion invalida. Intente de nuevo.")
```

---

### 🛠️ Comandos para crear el ejecutable

Instalar PyInstaller (una sola vez):

```
pip install pyinstaller
```

Compilar a ejecutable:

```
pyinstaller --onefile main.py
python3 -m PyInstaller --onefile main.py
```

El ejecutable quedará en la carpeta:

```
./dist/main

dist/main.exe
```

