

---
titulo: "L1 · Primer contacto — Tarea puente"
curso: "[[Introducción a Ciencias de la Computación]]"
sesion: "Entre Sesión 1 y Sesión 2"
modalidad: En equipo
integrantes:
  - Santiago Ocampo
  - Miguel Ángel Marín Castillo
base_asignada: 6
tags:
  - computacion/laboratorio
  - bases-numericas
  - recursion
  - arecibo
estado: 🟡 En progreso
---

# L1 · Primer contacto — Tarea puente
> **Entre la sesión 1 y la sesión 2 · Por equipo**

> [!abstract]+ Resumen y advertencia de la tarea
> La guía se divide en tres partes:
> - **Parte A:** No creerle al profesor; reproducir los resultados científicamente.
> - **Parte B:** Construir las herramientas matemáticas y de código para la sesión 2.
> - **Parte C:** Diseñar nuestra especie y su mensaje interestelar.
> 
> > [!danger] ¡Plazo estricto!
> > **La Parte C tiene que estar hecha antes de la sesión 2**: allá no hay tiempo de diseñar.

```python
EQUIPO = ["santiago ocampo", "miguel angel marin castillo", "", ""]
```

---

## Parte A · No me creas: reproducirlo

El archivo `datos/arecibo.txt` del curso lo congeló el profesor con el programa `congelar_arecibo.py` del repositorio de laboratorios. ¿Y por qué habrían de creerle? En ciencia la confianza no se pide: **se reproduce**.

> [!todo] Pasos a seguir
> 1. Descarguen el programa y córranlo ustedes, como lo harían en una terminal *(en Colab, `!` ejecuta comandos en la terminal de Linux)*.
> 2. El programa trae el mensaje de su fuente original y escribe su propia copia. Comparen **su** huella con la del `README` del curso.
> 3. Si no cuadra, no está *"casi bien"*: hay una diferencia, y encontrarla es la tarea.

### 1. Descarga y congelamiento

```python
REPO_RAW = "[https://raw.githubusercontent.com/cmorregof/labs-calculo-2026-2/main](https://raw.githubusercontent.com/cmorregof/labs-calculo-2026-2/main)"
!wget -q -O congelar_arecibo.py {REPO_RAW}/introduccion_cc/congelar_arecibo.py
!python congelar_arecibo.py --salida mi_arecibo.txt
```

> [!terminal]- Salida de consola *(clic para desplegar)*
> ```text
> descargando [https://en.wikipedia.org/w/index.php?title=Arecibo_message&action=raw](https://en.wikipedia.org/w/index.php?title=Arecibo_message&action=raw)
> 
> Congelado en mi_arecibo.txt
>   caracteres : 1679
>   unos       : 397
>   ceros      : 1282
>   SHA-256    : 24babf56f872fc2c38c940b4a438811be454f04a041fe56b3c239662f261dd66
> 
> Copien estas tres cifras en la sección 'Cifras de control' del README.
> ```

### 2. Verificación de integridad (`SHA-256`)

```python
import hashlib

with open("mi_arecibo.txt") as f:
    mio = f.read().strip()

# Cifras de control del README de introduccion_cc:
UNOS_README = 397
SHA_README = "24babf56f872fc2c38c940b4a438811be454f04a041fe56b3c239662f261dd66"

print("Mis unos:", mio.count("1"), "  README:", UNOS_README)
print("Mi SHA  :", hashlib.sha256(mio.encode("ascii")).hexdigest())
print("README  :", SHA_README)
print("¿Idénticos?", hashlib.sha256(mio.encode("ascii")).hexdigest() == SHA_README)
```

> [!success] Resultado de comparación
> ```text
> Mis unos: 397   README: 397
> Mi SHA  : 24babf56f872fc2c38c940b4a438811be454f04a041fe56b3c239662f261dd66
> README  : 24babf56f872fc2c38c940b4a438811be454f04a041fe56b3c239662f261dd66
> ¿Idénticos? True
> ```

> [!question] Pregunta abierta *(Pendiente de responder en el Colab)*
> **Cinco líneas:** ¿por qué comparar dos huellas de 64 caracteres es una manera confiable de saber que dos archivos de 1 679 caracteres son idénticos? ¿Qué tendría que pasar para que dos mensajes distintos tuvieran la misma huella?
> 
> > *Borrador de respuesta:* ...

---

## Parte B · Dos maneras de cambiar de base

> [!note] Intuición matemática
> En clase escribieron $M(n) = M(\lfloor n/10 \rfloor) + n \bmod 10$, con $M(0) = 0$ (suma de cifras). Esa recursión **separa la última cifra con `mod` y le pasa el resto a sí misma con `div`**.
> 
> Cambiar de base es exactamente lo mismo, con dos diferencias:
> 1. En vez de $10$ se usa la base $b$.
> 2. En vez de **sumar** las cifras, se **ponen en fila**.
> 
> $$\text{dígitos}_b(n) = \text{dígitos}_b(n \operatorname{div} b) \mathbin{\Vert} [n \bmod b]$$

### 1. Implementación en Python

- `a_base_ciclo(n, b)`: versión iterativa con `while` *(ejercicio 8 de Falsos amigos)*.
- `a_base_rec(n, b)`: versión recursiva *(cuidando que la llamada se achique hacia el caso base)*.
- `de_base(digitos, b)`: función inversa *(es `leer_numero` de la sesión 1 generalizada a base $b$)*.

```python
def a_base_ciclo(n, b):
    """CONTRATO
    Recibe: un entero n >= 0 y una base b >= 2.
    Devuelve: la lista de dígitos de n en base b, el más significativo primero.
              a_base_ciclo(0, b) devuelve [0].
    """
    if n == 0:
        return [0]

    digitos = []
    while n > 0:
        digitos.append(n % b)  # Saca el último dígito en base b (mod)
        n = n // b             # Actualiza n con el cociente entero (div)

    digitos.reverse()          # Invierte la lista para dejar el más significativo primero
    return digitos


def a_base_rec(n, b):
    """Mismo contrato, escrita de forma recursiva."""
    if n < b:
        return [n]
    return a_base_rec(n // b, b) + [n % b]


def de_base(digitos, b):
    """CONTRATO
    Recibe: una lista de dígitos en base b, el más significativo primero.
    Devuelve: el entero que representan.
    """
    numero = 0
    for d in digitos:
        numero = numero * b + d
    return numero
```

### 2. Pruebas unitarias y aleatorias

```python
for f in (a_base_ciclo, a_base_rec):
    assert f(1974, 6) == [1, 3, 0, 5, 0], f.__name__
    assert f(13, 2) == [1, 1, 0, 1], f.__name__
    assert f(255, 16) == [15, 15], f.__name__
    assert f(0, 7) == [0], f.__name__
    assert f(5, 6) == [5], f.__name__
assert de_base([1, 3, 0, 5, 0], 6) == 1974

import random
for _ in range(1000):
    n, b = random.randint(0, 10**6), random.randint(2, 16)
    assert a_base_ciclo(n, b) == a_base_rec(n, b)
    assert de_base(a_base_rec(n, b), b) == n
print("✔ Las dos maneras coinciden en 1000 casos al azar, y de_base las deshace.")
```

> [!success] Test aprobado
> `✔ Las dos maneras coinciden en 1000 casos al azar, y de_base las deshace.`

### 3. Tabla de seguimiento y análisis

> [!example] Traza de la pila: `a_base_rec(1974, 6)`
> Una fila por llamada, mostrando qué queda esperando en la pila hasta alcanzar el caso base:
> 
> | Llamada | `n` | `n // 6` | `n % 6` | Devuelve *(al regresar de la recursión)* |
> | :---: | :---: | :---: | :---: | :--- |
> | **1** | `1974` | `329` | `0` | `[1, 3, 0, 5] + [0]` = `[1, 3, 0, 5, 0]` |
> | **2** | `329` | `54` | `5` | `[1, 3, 0] + [5]` = `[1, 3, 0, 5]` |
> | **3** | `54` | `9` | `0` | `[1, 3] + [0]` = `[1, 3, 0]` |
> | **4** | `9` | `1` | `3` | `[1] + [3]` = `[1, 3]` |
> | **5** *(base)* | `1` | — | — | `[1]` *(porque $1 < 6$)* |

> [!quote] ¿Cuál de las dos maneras prefieren y por qué?
> Preferimos la **recursiva** porque es más corta, limpia y traduce directamente la definición matemática sin variables auxiliares. A diferencia del `while`, no necesita usar `reverse()` al final porque la pila ordena los dígitos automáticamente al devolverse, y además resuelve `n = 0` de forma natural en su caso base (`n < b`).

---

## Parte C · Su especie

> [!info] Reglas del mundo
> El monitor lanza un dado y le asigna a cada equipo una **base**: `2, 3, 5, 6, 8` o `12`. Esa es su manera de contar. **Todos los números de su mensaje irán en esa base.** *(Como el ingeniero de Project Hail Mary, que cuenta en base 6).*

```python
MI_BASE = None          # Asignar 6 (según el contrato de lectura)
NOMBRE_ESPECIE = ""     # Pendiente definir nombre
```

### Estructura del mensaje interestelar

Elijan **tres o cuatro secciones** (en papel primero):
1. **Contar *(obligatoria)*:** `1, 2, 3, …` escritos en su base. Quien sepa leer ese conteo descubre su base al ver cuándo se acaban los dígitos y aparece un `"10"`.
2. **Identidad:** ¿Quiénes son? *(Siglas de la universidad: UNAL)*.
3. **Entorno y monumentos:** ¿Dónde viven y qué les importa? *(Manizales a 2 150 m, arquitectura local)*.

### El contrato de lectura

> [!warning] ¡Confidencial!
> Este contrato **es su predicción** para la sesión 2: allá veremos cuántos de estos pasos dio de verdad la otra especie. **No se lo muestren a nadie.**

> [!tip] Predicción paso a paso del equipo *(Base 6 · Matriz $43 \times 19$)*
> 1. **Darse cuenta de la geometría del mensaje:** Contar el total de bits ($817$), factorizarlo en sus dos números primos ($43 \times 19$) y organizar la cadena en una cuadrícula de **19 columnas de ancho por 43 filas de alto** *(descartando la orientación invertida al notar que las formas se alinean verticalmente)*.
> 2. **Identificar la estructura por bloques:** Detectar las filas completamente vacías (llenas de ceros) que actúan como separadores horizontales y dividen el rectángulo en **4 secciones temáticas**.
> 3. **Descubrir nuestra base numérica (base 6):** Leer la primera sección por columnas de arriba hacia abajo (reconociendo el bit inferior de la fila 6 como marca de referencia y los dos grupos superiores de 3 bits como dígitos), observar la secuencia $1, 2, 3, 4, 5, 10, 11$ y deducir que contamos en **base 6** porque después del $5$ aparece el $10$.
> 4. **Reconocer nuestra identidad escrita:** Interpretar la segunda sección como un mapa de bits gráfico que dibuja cuatro letras (**UNAL**), deduciendo que corresponde a la sigla o nombre de nuestra institución de origen.
> 5. **Interpretar los monumentos de nuestro entorno:** Reconocer en las secciones 3 y 4 las siluetas arquitectónicas de nuestra ciudad: dos torres verticales (*Torre al Cielo de Chipre* y *Torre de El Cable*) seguidas de un edificio con aguja central alta y un recinto ovalado cerrado (*Catedral Basílica* y *Estadio Palogrande de Manizales*).

---

## Checklist de entrega

> [!check] Antes de irse
> - [ ] **Responder la pregunta de SHA-256** en la Parte A *(5 líneas)*.
> - [ ] **Rellenar variables** `MI_BASE = 6` y `NOMBRE_ESPECIE` en la Parte C.
> - [ ] **Ejecutar todo de arriba a abajo** en Colab (*Entorno de ejecución → Reiniciar y ejecutar todo*). Si algo falla, no está terminado.
> - [ ] **Guardar una copia en GitHub** en su repositorio `intro-cc` (*Archivo → Guardar una copia en GitHub*). Cada integrante en el suyo.
> - [ ] **Bitácora de IA (individual):** Completar la plantilla `bitacora_IA_L1.md` ubicada en la carpeta `introduccion_cc` del repositorio de laboratorios.