
> [!question]
> # L1 · Primer contacto — Sesión 1: **Recibir**
> ### Introducción a las Ciencias de la Computación · UNAL Manizales
>
> El 16 de noviembre de 1974, el radiotelescopio de Arecibo, en Puerto Rico, mandó hacia el cúmulo de estrellas M13 un mensaje de **1 679 bits**. Sin idioma, sin instrucciones, sin un "léase así". Solo ceros y unos.
>
> Hoy ustedes son los extraterrestres que lo reciben.
>
> **Cómo se trabaja en este cuaderno**
>
> - Las preguntas de **Momento 1** se responden **en papel, antes de tocar el computador**. Tómenle foto y súbanla al final del cuaderno. No hay predicción mala: hay predicción no escrita.
> - Cada herramienta es una **función**, y cada función se escribe **primero su contrato** (qué recibe, qué devuelve, qué supone) y **después** su código.
> - Debajo de cada función hay una celda de **pruebas** con `assert`. Si una prueba falla, la celda se queja con un `AssertionError`. Eso no es un regaño: es la máquina diciéndoles exactamente dónde mirar.
> - Las celdas que dicen **CAJA** ya vienen hechas. Pueden abrirlas, pero no hace falta: basta con su contrato. Así usan todos los días `print()` o `range()`.
> - Donde dice `# TU CÓDIGO AQUÍ`, escriben ustedes. Donde dice ✍️, responden en texto.

> [!code] Código
> ```python
> # Integrantes del equipo (nombre completo)
> EQUIPO = ["miguel angel marin castillo", "satiago ocampo", "", ""]
> ```

> [!question]
> ## Momento 1 — Predecir *(en papel, 15 minutos)*
>
> **P1.** Les llega una cadena de 1 679 ceros y unos, sin nada más. ¿Qué es lo primero que intentarían para encontrarle sentido? Escriban tres ideas, en orden.
>
> **P2.** Si alguien quisiera mandar un **dibujo** con bits, ¿cómo lo haría? ¿Qué tendría que adivinar quien lo recibe?
>
> **P3.** ¿De cuántas maneras se pueden acomodar 1 679 cuadritos en un rectángulo completo, sin que sobre ni falte ninguno? Sin computador: con lo que saben de divisores.
>
> ✍️ *Copien aquí sus respuestas cortas (la foto del papel va al final):*
>
> - **P1:**
>   1. Revisar cuántos ceros y unos tiene el mensaje y buscar patrones o secuencias que se repitan.
>   2. Intentar organizar los bits en filas y columnas para observar si forman una imagen.
>   3. Buscar relaciones matemáticas entre los bits para identificar números o información codificada.
> - **P2:** Convertiría el dibujo en una cuadrícula de píxeles, donde cada píxel se representa con un bit (`1` representa un píxel negro y `0` representa un píxel blanco). Quien recibe el mensaje tendría que descubrir cuántas filas y columnas tiene la imagen, en qué dirección debe leer los bits y qué significa cada valor.
> - **P3:** Hay cuatro maneras, considerando las orientaciones: `1 × 1679`, `23 × 73`, `73 × 23` y `1679 × 1`. Como `1679 = 23 × 73` y ambos factores son primos, solamente existen esos cuatro pares ordenados (siendo `23 × 73` y `73 × 23` las formas rectangulares interesantes).

> [!note]
> ## 0. Traer el mensaje
>
> La celda que sigue es una **caja**. Su contrato es una línea: `cargar_mensaje()` devuelve una cadena de 1 679 caracteres, cada uno `'0'` o `'1'`. Si ya tienen el archivo `datos/arecibo.txt`, lo lee de ahí; si no, lo descarga de la copia congelada del curso o de la fuente original.
>
> También trae `huella(bits)`, que devuelve cuántos unos tiene el mensaje y su **SHA-256**: una "huella digital" de 64 caracteres. Si cambia un solo bit del mensaje, la huella cambia por completo. Es la misma idea con la que su banco sabe que nadie alteró una transferencia en el camino.

> [!code] Código
> ```python
> # ==========================================================================
> #  CAJA: traer el mensaje de Arecibo.  No hace falta abrirla; sí entender su contrato.
> #  cargar_mensaje() -> str   Devuelve los 1679 caracteres '0'/'1' del mensaje.
> #  Busca, en este orden: (1) el archivo datos/arecibo.txt si ya lo tienen,
> #  (2) la copia congelada del repositorio de laboratorios, (3) la fuente original (Wikipedia).
> # ==========================================================================
> import os, re, html, hashlib, urllib.request
>
> REPO_RAW = "https://raw.githubusercontent.com/cmorregof/labs-calculo-2026-2/main"
> LONGITUD = 1679
>
> def _descargar(url):
>     pedido = urllib.request.Request(url, headers={"User-Agent": "UNAL-IntroCC-L1/1.0"})
>     with urllib.request.urlopen(pedido, timeout=30) as r:
>         return r.read().decode("utf-8", errors="replace")
>
> def _extraer_bits(texto):
>     if re.search(r"<tr\b", texto, flags=re.I):
>         filas = [re.sub(r"\s", "", html.unescape(re.sub(r"<[^>]+>", "", "<" + re.split(r"</tr>", t, flags=re.I)[0])))
>                  for t in re.split(r"<tr\b", texto, flags=re.I)[1:]]
>         actual = []
>         for f in filas + ["x"]:
>             if f and set(f) <= {"0", "1"}:
>                 actual.append(f)
>             else:
>                 if len("".join(actual)) == LONGITUD:
>                     return "".join(actual)
>                 actual = []
>     texto = html.unescape(re.sub(r"<[^>]+>", "\n", texto))
>     bloques, actual = [], []
>     for renglon in texto.splitlines():
>         limpio = re.sub(r"\s", "", renglon)
>         if limpio == "":
>             continue
>         if set(limpio) <= {"0", "1"}:
>             actual.append(limpio)
>         else:
>             if actual:
>                 bloques.append("".join(actual))
>             actual = []
>     if actual:
>         bloques.append("".join(actual))
>     for b in bloques:
>         if len(b) == LONGITUD:
>             return b
>     return None
>
> def cargar_mensaje():
>     for local in ("datos/arecibo.txt", "../datos/arecibo.txt"):
>         if os.path.exists(local):
>             with open(local) as f:
>                 print("Mensaje leído de", local)
>                 return f.read().strip()
>     intentos = [REPO_RAW + "/datos/arecibo.txt",
>                 "https://en.wikipedia.org/w/index.php?title=Arecibo_message&action=raw",
>                  "https://en.wikipedia.org/wiki/Arecibo_message"]
>     for url in intentos:
>         try:
>             bits = _extraer_bits(_descargar(url))
>             if bits:
>                 os.makedirs("datos", exist_ok=True)
>                 with open("datos/arecibo.txt", "w") as f:
>                     f.write(bits)
>                 print("Mensaje descargado de", url)
>                 return bits
>         except Exception as e:
>             print("No se pudo con", url, "->", e)
>     raise RuntimeError("No pude traer el mensaje. Avísenle al monitor: hay una copia de respaldo.")
>
> def huella(bits):
>     """Devuelve (número de unos, SHA-256). Si cambia un solo bit, el SHA-256 cambia por completo."""
>     return bits.count("1"), hashlib.sha256(bits.encode("ascii")).hexdigest()
> ```

> [!code] Código
> ```python
> bits = cargar_mensaje()
> unos, sha = huella(bits)
> print("Unos:", unos)
> print("Huella SHA-256:", sha)
> print("Comparen estas dos cifras con las del README de introduccion_cc en el repositorio de laboratorios.")
> ```

> [!question]
> ## 1. Conocer el dato
>
> Antes de calcular nada, se mira. Un dato que no se miró antes de usarlo es un dato en el que se confió sin razón. Contesten **con código**:
>
> - ¿Cuántos caracteres tiene `bits`? (`len`)
> - ¿Qué caracteres **distintos** aparecen? (`set`)
> - ¿Cuántos unos y cuántos ceros hay? (`.count`)
> - Miren los primeros 60 caracteres. ¿Ven algún patrón a simple vista?

> [!code] Código
> ```python
> n_caracteres = len(bits)
> distintos = set(bits)
> n_unos = bits.count("1")
> n_ceros = bits.count("0")
>
> print("caracteres:", n_caracteres)
> print("distintos :", distintos)
> print("unos/ceros:", n_unos, n_ceros)
> print(bits[:60])
> ```

> [!code] Código
> ```python
> # Pruebas
> assert n_caracteres == 1679, "¿Seguro que contaron bien? El mensaje tiene 1679 bits."
> assert distintos == {"0", "1"}, "Debería haber solo ceros y unos."
> assert n_unos + n_ceros == n_caracteres
> print("✔ Conocen el dato.")
> ```

> [!question]
> **Una ironía para pensar.** En el archivo, cada bit del mensaje está guardado como un **carácter**, y en el disco un carácter ocupa 8 bits. Corran la celda: ¿cuántos bits ocupa en su computador el mensaje de 1 679 bits? Representar algo siempre cuesta algo, y lo que cuesta depende de la convención que se eligió.

> [!code] Código
> ```python
> bits_en_disco = len(bits.encode("ascii")) * 8
> print("El mensaje tiene", len(bits), "bits de información y ocupa", bits_en_disco, "bits en el disco.")
> ```

> [!question]
> ✍️ *¿Cuál es la diferencia entre el carácter `'1'` y el bit 1? Dos líneas.*
>
> El carácter `'1'` es un símbolo de texto que se almacena mediante una codificación como ASCII y ocupa 8 bits (1 byte) en memoria. El bit `1` es la unidad mínima de información binaria que representa directamente uno de los dos estados posibles (`0` o `1`).

> [!note]
> ## 2. La llave: `es_primo(n)`
>
> En la clase 5 escribieron esto en PSeInt:
>
> ```
> esPrimo <- (n >= 2);
> d <- 2;
> Mientras d * d <= n y esPrimo Hacer
>     Si n mod d = 0 Entonces
>         esPrimo <- Falso;
>     FinSi
>     d <- d + 1;
> FinMientras
> ```
>
> Tradúzcanlo a Python **como función**. Recuerden el diccionario: `mod` es `%`, `y` es `and`, `Falso` es `False`, y lo que va dentro del `while` lo dice la sangría. La función **devuelve** el resultado con `return`; no lo imprime.

> [!code] Código
> ```python
> def es_primo(n):
>     """CONTRATO
>     Recibe: un entero n.
>     Devuelve: True si n es primo, False si no.
>     Supone: n >= 0.
>     """
>     esPrimo = (n >= 2)
>     d = 2
>     while d * d <= n and esPrimo:
>         if n % d == 0:
>             esPrimo = False
>         d = d + 1
>     return esPrimo
> ```

> [!code] Código
> ```python
> # Pruebas
> assert es_primo(2) and es_primo(3) and es_primo(97) and es_primo(1000003)
> assert not es_primo(0) and not es_primo(1) and not es_primo(91) and not es_primo(1000001)
> print("✔ es_primo pasa las pruebas.")
> print("¿1679 es primo?", es_primo(1679))
> ```

> [!note]
> ## 3. Las formas posibles: `rectangulos(n)`
>
> Un rectángulo de `filas × columnas` cuadritos usa exactamente `n` cuadritos cuando `filas * columnas == n`. La función devuelve **todas** las parejas posibles, ordenadas por número de filas.
>
> Ejemplo: `rectangulos(12)` debe devolver `[(1, 12), (2, 6), (3, 4), (4, 3), (6, 2), (12, 1)]`.

> [!code] Código
> ```python
> def rectangulos(n):
>     """CONTRATO
>     Recibe: un entero n >= 1.
>     Devuelve: la lista de parejas (filas, columnas) con filas * columnas == n,
>               ordenada de menor a mayor número de filas.
>     """
>     resultado = []
>     for filas in range(1, n + 1):
>         if n % filas == 0:
>             columnas = n // filas
>             resultado.append((filas, columnas))
>     return resultado
> ```

> [!code] Código
> ```python
> # Pruebas
> assert rectangulos(12) == [(1, 12), (2, 6), (3, 4), (4, 3), (6, 2), (12, 1)]
> assert rectangulos(7) == [(1, 7), (7, 1)]
> assert len(rectangulos(1680)) == 40
> print("✔ rectangulos pasa las pruebas.")
> print("Formas de acomodar 1679:", rectangulos(1679))
> print("Formas de acomodar 1680:", len(rectangulos(1680)))
> ```

> [!question]
> ✍️ **La pregunta de fondo** (va al informe). ¿Por qué quien diseñó el mensaje eligió un número que es producto de **dos primos**? ¿Qué habría pasado si el mensaje tuviera 1 680 bits, que se pueden acomodar de 40 maneras?
>
> Se eligió 1679 porque es el producto de dos números primos diferentes: 23 y 73. Esto restringe las formas no triviales de organizar el mensaje a solo dos opciones: un rectángulo de 23 filas por 73 columnas, o de 73 filas por 23 columnas. Si el mensaje tuviera 1680 bits, existirían 40 maneras distintas de acomodarlo en rectángulos, lo que dificultaría enormemente que el receptor descubra cuál es la dimensión bidimensional correcta.
>
> **Para quien termine antes: mejorar el algoritmo.** Su `rectangulos` probablemente prueba todos los números de 1 a `n`. Con la idea de la clase 5 basta probar hasta $\sqrt{n}$: cada divisor `d` pequeño trae de regalo a su pareja `n // d`. Escriban `rectangulos_rapido(n)`, cuenten las vueltas de cada versión con `n = 1679` y con `n = 1 000 000`, y comprueben que las dos devuelven lo mismo.

> [!code] Código
> ```python
> # (opcional) rectangulos_rapido(n) y conteo de vueltas
> def rectangulos_rapido(n):
>     pequenos = []
>     grandes = []
>     d = 1
>     vueltas = 0
>     while d * d <= n:
>         vueltas += 1
>         if n % d == 0:
>             pequenos.append((d, n // d))
>             if d != n // d:
>                 grandes.append((n // d, d))
>         d += 1
>     return pequenos + grandes[::-1], vueltas
>
> def rectangulos_con_vueltas(n):
>     resultado = []
>     vueltas = 0
>     for filas in range(1, n + 1):
>         vueltas += 1
>         if n % filas == 0:
>             resultado.append((filas, n // filas))
>     return resultado, vueltas
>
> for n in (1679, 1000000):
>     normal, vueltas_normal = rectangulos_con_vueltas(n)
>     rapido, vueltas_rapido = rectangulos_rapido(n)
>     assert normal == rapido
>     print(f"Número: {n} | Vueltas normal: {vueltas_normal} | Vueltas rápido: {vueltas_rapido}")
> ```

> [!note]
> ## 4. Ver: `dibujar(bits, columnas)`
>
> Ahora sí: acomodar los bits en filas y mirarlos. Lo hacemos en dos funciones, porque una función que **devuelve** se puede probar, y una que solo **imprime**, no.
>
> - `filas_de(bits, columnas)` devuelve la lista de renglones, cada uno una cadena de `columnas` caracteres. Ejemplo: `filas_de("101100", 3)` es `["101", "100"]`.
> - `dibujar(bits, columnas)` usa `filas_de` e imprime cada renglón cambiando `"1"` por `"█"` y `"0"` por un espacio.

> [!code] Código
> ```python
> def filas_de(bits, columnas):
>     """CONTRATO
>     Recibe: una cadena de bits y un número de columnas que divide su longitud.
>     Devuelve: la lista de renglones, cada uno de longitud 'columnas'.
>     """
>     resultado = []
>     for i in range(0, len(bits), columnas):
>         fila = bits[i:i + columnas]
>         resultado.append(fila)
>     return resultado
>
>
> def dibujar(bits, columnas):
>     """CONTRATO
>     Recibe: una cadena de bits y un número de columnas.
>     Devuelve: nada. Imprime los renglones con '█' para 1 y ' ' para 0.
>     """
>     for fila in filas_de(bits, columnas):
>         dibujo = fila.replace("1", "█").replace("0", " ")
>         print(dibujo)
> ```

> [!code] Código
> ```python
> # Pruebas
> assert filas_de("101100", 3) == ["101", "100"]
> assert filas_de("101100", 2) == ["10", "11", "00"]
> assert len(filas_de(bits, 23)) == 73
> print("✔ filas_de pasa las pruebas.")
> ```

> [!note]
> Prueben las dos formas que encontraron. **Una de las dos es ruido; la otra no.** Este es el momento de dejarlo correr y esperar a ver si coincide con lo que pensaban.

> [!code] Código
> ```python
> dibujar(bits, 73)
> ```

> [!code] Código
> ```python
> dibujar(bits, 23)
> ```

> [!note]
> La caja que sigue dibuja lo mismo como imagen, con la cuadrícula numerada. Les va a servir para ubicar filas y columnas en el paso 5.

> [!code] Código
> ```python
> # CAJA: dibujo con cuadrícula numerada, para encontrar filas y columnas.
> # dibujar_imagen(bits, columnas) -> None   Muestra los bits como imagen: 1 = negro, 0 = blanco.
> import matplotlib.pyplot as plt
>
> def dibujar_imagen(bits, columnas, alto=12):
>     filas = len(bits) // columnas
>     matriz = [[int(bits[f * columnas + c]) for c in range(columnas)] for f in range(filas)]
>     fig, ax = plt.subplots(figsize=(alto * columnas / max(filas, 1) + 1, alto))
>     ax.imshow(matriz, cmap="Greys", interpolation="nearest")
>     ax.set_xticks(range(0, columnas, 5)); ax.set_yticks(range(0, filas, 5))
>     ax.set_xticks([x - 0.5 for x in range(columnas + 1)], minor=True)
>     ax.set_yticks([y - 0.5 for y in range(filas + 1)], minor=True)
>     ax.grid(which="minor", color="#dddddd", linewidth=0.4)
>     ax.set_xlabel("columna"); ax.set_ylabel("fila")
>     ax.set_title(f"{filas} filas x {columnas} columnas")
>     plt.show()
> ```

> [!code] Código
> ```python
> dibujar_imagen(bits, 23)
> ```

> [!note]
> ## 5. Leer: `leer_numero(columna)`
>
> En la parte de arriba del dibujo hay números escritos en **binario**. Recordemos cómo se lee un número en base 2: cada posición vale el doble de la que tiene a la derecha.
>
> $$1011_2 = 1\cdot 8 + 0\cdot 4 + 1\cdot 2 + 1\cdot 1 = 11$$
>
> Hay una manera de leerlo con un ciclo y sin potencias, de izquierda a derecha: arrancar en 0 y, por cada bit, **duplicar lo que llevo y sumarle el bit**. Con `1011`: 0 → 1 → 2 → 5 → 11. Es un acumulador, como el de Gauss. Hagan la tabla de seguimiento en papel antes de escribirla.

> [!code] Código
> ```python
> def leer_numero(lista_bits):
>     """CONTRATO
>     Recibe: una lista de enteros 0 y 1; el primero es el más significativo.
>     Devuelve: el entero que representan en base 2.
>     """
>     numero = 0
>     for bit in lista_bits:
>         numero = numero * 2 + bit
>     return numero
> ```

> [!code] Código
> ```python
> # Pruebas
> assert leer_numero([1, 0, 1, 1]) == 11
> assert leer_numero([0]) == 0 and leer_numero([1]) == 1
> assert leer_numero([1, 0, 0, 0, 0, 0, 0, 0]) == 128
> assert leer_numero([0, 0, 1, 1]) == 3
> print("✔ leer_numero pasa las pruebas.")
> ```

> [!note]
> Los números del mensaje están escritos **en vertical**, en columnas. Hace falta una herramienta para sacar un pedazo de columna de la cuadrícula.

> [!code] Código
> ```python
> def columna(bits, columnas, j, fila_desde, fila_hasta):
>     """CONTRATO
>     Recibe: los bits, el ancho de la cuadrícula, una columna j y un rango de filas.
>     Devuelve: la lista de enteros (0/1) de la columna j, desde fila_desde hasta fila_hasta - 1,
>               de arriba hacia abajo.
>     """
>     resultado = []
>     for f in range(fila_desde, fila_hasta):
>         posicion = f * columnas + j
>         resultado.append(int(bits[posicion]))
>     return resultado
> ```

> [!code] Código
> ```python
> # Pruebas (una cuadrícula de juguete de 3 x 4)
> juguete = "1000" + "0101" + "1101"
> assert columna(juguete, 4, 0, 0, 3) == [1, 0, 1]
> assert columna(juguete, 4, 3, 1, 3) == [1, 1]
> print("✔ columna pasa las pruebas.")
> ```

> [!note]
> Y una caja para mirar de cerca una región, con los números de fila y de columna al borde:

> [!code] Código
> ```python
> # CAJA: mirar de cerca una región.  recorte(bits, columnas, f0, f1, c0, c1) imprime
> # las filas f0..f1-1 y las columnas c0..c1-1, con los números de fila y de columna al borde.
> def recorte(bits, columnas, f0, f1, c0, c1):
>     print("     " + "".join(str(c % 10) for c in range(c0, c1)))
>     for f in range(f0, f1):
>         fila = bits[f * columnas:(f + 1) * columnas]
>         print(f"{f:3d}  " + "".join("█" if ch == "1" else "·" for ch in fila[c0:c1]))
> ```

> [!code] Código
> ```python
> recorte(bits, 23, 0, 12, 0, 23)    # las primeras 12 filas, todas las columnas
> ```

> [!question]
> ### Descifrar
>
> 1. **La primera sección.** Ubiquen, en las primeras filas, las columnas que forman números. Extráiganlas con `columna(...)` y léanlas con `leer_numero(...)`. Hay **una sola dirección** de lectura (de arriba abajo, o de abajo arriba con `lista[::-1]`) que hace que la sección diga algo que reconocen. Encuéntrenla probando las dos.
>    - Ojo: puede haber bits que **no** son parte del número, sino marcas que dicen "aquí hay un número". ¿Hay una fila con un 1 debajo de cada número?
>    - Algunos números pueden ocupar más de una columna. ¿Cuáles, y por qué?
> 2. **La segunda sección.** Un poco más abajo hay cinco números más. Léanlos igual y búsquenlos en la tabla periódica de la celda siguiente.

> [!code] Código
> ```python
> # Los primeros 20 elementos de la tabla periódica, por número atómico
> TABLA_PERIODICA = {1: "H hidrógeno", 2: "He helio", 3: "Li litio", 4: "Be berilio", 5: "B boro",
>                    6: "C carbono", 7: "N nitrógeno", 8: "O oxígeno", 9: "F flúor", 10: "Ne neón",
>                    11: "Na sodio", 12: "Mg magnesio", 13: "Al aluminio", 14: "Si silicio",
>                    15: "P fósforo", 16: "S azufre", 17: "Cl cloro", 18: "Ar argón",
>                    19: "K potasio", 20: "Ca calcio"}
> ```

> [!code] Código
> ```python
> # Primera sección: números del 1 al 10 (filas 0 a 2; la fila 3 es marca de posición)
> cols_1_a_7 = [0, 2, 4, 6, 8, 10, 12]
> numeros_1_7 = [leer_numero(columna(bits, 23, j, 0, 3)) for j in cols_1_a_7]
> numeros_8_10 = [
>     leer_numero(columna(bits, 23, der, 0, 3) + columna(bits, 23, izq, 0, 3))
>     for izq, der in ((14, 15), (17, 18), (20, 21))
> ]
> print("Primera sección (arriba a abajo):", numeros_1_7 + numeros_8_10)
> print("Primera sección (abajo a arriba):", [leer_numero(columna(bits, 23, j, 0, 3)[::-1]) for j in cols_1_a_7])
>
> # Segunda sección: elementos químicos (filas 5 a 8; la fila 9 es marca de posición)
> atomicos = [leer_numero(columna(bits, 23, j, 5, 9)) for j in range(9, 14)]
> print("Segunda sección (números atómicos):", atomicos)
> print("Elementos:", [TABLA_PERIODICA[z] for z in atomicos])
> ```

> [!question]
> ✍️ **Lo que leyeron:**
>
> - Primera sección (filas **0** a **3**): los números **1, 2, 3, 4, 5, 6, 7, 8, 9 y 10 (la fila 3 funciona como marca de inicio/fin de cada número, y el 8, 9 y 10 ocupan dos columnas porque necesitan más de 3 bits, llevando la columna derecha los bits más significativos)**. Dirección de lectura: **de arriba hacia abajo**, porque **así se obtiene la secuencia numérica ordenada y reconocible del 1 al 10**.
> - Segunda sección (filas **4** a **9**): los números **1, 6, 7, 8 y 15**, que corresponden a **Hidrógeno (H), Carbono (C), Nitrógeno (N), Oxígeno (O) y Fósforo (P)**. ¿De qué molécula de la vida son esos elementos? **Forman parte de la estructura fundamental del ADN (ácido desoxirribonucleico).**

> [!question]
> ## 6. Inventario
>
> Con el dibujo y sus funciones, hagan la lista de lo que creen que dice cada parte del mensaje. **No hace falta descifrarlo todo**: hace falta separar lo que descifraron **con código** de lo que están adivinando **con los ojos**.
>
> | Filas | Qué creemos que dice | ¿Lo leímos con código o lo adivinamos? | ¿Cómo lo comprobaríamos? |
> |---|---|---|---|
> | 0–3 | Números del 1 al 10 | Lo leímos con código | Convirtiendo cada columna de bits a base decimal con `leer_numero()` |
> | 4–9 | Elementos H, C, N, O y P | Lo leímos con código | Extrayendo los valores binarios y comparándolos con números atómicos |
> | 10–29 | Fórmulas químicas de los componentes del ADN | Código e interpretación visual | Leyendo las cantidades de átomos por columna y verificando fórmulas |
> | 25–45 | Doble hélice del ADN y barra central | Lo adivinamos con los ojos (visual) | Comparando la silueta con la estructura helicoidal del ADN |
> | 45–54 | Ser humano, estatura y población | Código e interpretación visual | Reconociendo la silueta humana y decodificando los valores laterales |
> | 55–59 | Sistema solar (el Sol y los planetas) | Lo adivinamos con los ojos (visual) | Comparando los tamaños relativos y la posición desplazada del tercer planeta |
> | 60–72 | Radiotelescopio de Arecibo | Interpretación visual y código | Analizando la figura parabólica y decodificando su diámetro inferior |

> [!question]
> ## Momento 3 — Explicar *(media página por equipo)*
>
> **La lista de convenciones.** El emisor supuso que el receptor ya sabía ciertas cosas. Escríbanlas todas las que encuentren, y para cada una decidan si un extraterrestre de verdad podría **deducirla** o tendría que **adivinarla**.
>
> | Convención que supuso el emisor | ¿Se puede deducir o hay que adivinarla? | ¿Por qué? |
> |---|---|---|
> | Los bits forman un rectángulo | Se puede deducir | Al factorizar 1679 en sus únicos divisores primos (`23 × 73`), solo hay dos matrices posibles y una produce patrones ordenados. |
> | El `1` es píxel activo y el `0` es fondo | Se puede deducir | El contraste entre fondo mayoritario (`0`) y figuras compactas (`1`) revela las formas, aunque invertir colores mantiene la forma. |
> | Los números están escritos en base 2 (binario) | Se puede deducir | Al observar la progresión secuencial del 1 al 10 en columnas, la aritmética binaria emerge de forma natural. |
> | Los números se leen verticalmente de arriba hacia abajo | Se puede deducir | Probar ambas direcciones demuestra que solo de arriba hacia abajo se forma una secuencia ordenada del 1 al 10. |
> | Existen bits de marca (fila inferior de cada número) | Se puede deducir | Aparecen repetidos sistemáticamente debajo de cada columna numérica sin alterar la progresión aritmética. |
> | Los números atómicos identifican elementos químicos | Se puede deducir | Cualquier civilización con conocimiento de física atómica ordena los elementos por su número de protones. |
> | Los dibujos representan objetos reales en proyección 2D | Hay que adivinarla / interpretarla | Depende de la percepción visual humana y de reconocer siluetas culturales (como un cuerpo humano o una antena). |
> | La Tierra está desplazada hacia arriba para indicar origen | Hay que adivinarla / interpretarla | Aunque la posición orbital es deducible, el significado del desplazamiento vertical es una metáfora visual. |
>
> **Confrontación.** ¿Qué predijeron en P1, P2 y P3, qué encontraron y a qué atribuyen la diferencia?
>
> En P1 predijimos que debíamos contar los bits, buscar patrones y organizarlos en una cuadrícula; en P2 planteamos que un dibujo se codifica como una matriz binaria donde el receptor debe descubrir las dimensiones y el sentido de lectura; y en P3 calculamos que 1679 admite cuatro rectángulos posibles (`1×1679`, `23×73`, `73×23` y `1679×1`). Al ejecutar el código encontramos exactamente que la matriz de `73 × 23` revela una secuencia estructurada de números, elementos químicos, el ADN, el ser humano, el sistema solar y el radiotelescopio. La diferencia entre nuestras predicciones iniciales y el hallazgo real radica en que al inicio desconocíamos las convenciones internas (como la lectura vertical de números, los bits de marca y la mezcla de datos numéricos con pictogramas).
>
> **La frase obligatoria**, con sus palabras: *"Representar es..."*
>
> ✍️ *"Representar es transformar información en símbolos o estructuras que permitan almacenarla, transmitirla e interpretarla mediante determinadas convenciones compartidas o deducibles."* En este laboratorio comprobamos que codificar datos no basta para garantizar su comprensión: quien recibe el mensaje necesita conocer o descubrir las reglas con las que fue construido.

> [!question]
> ## La IA como objeto de estudio *(para la bitácora individual)*
>
> Denle a su asistente (Claude, o el que tengan) **la cadena de 1 679 bits sin decirle qué es**, y pídanle que la descifre. Guarden la respuesta completa en la bitácora.
>
> ¿La **descifró** (hizo cuentas que ustedes pueden verificar con sus funciones) o la **reconoció** (sabía de antemano qué era y recitó)? Propongan una prueba para distinguirlo. Pista: en la sesión 2 van a tener un mensaje que no está en ningún lugar de internet.
>
> ✍️ *Análisis para la bitácora:* Para distinguir si la IA descifró o simplemente reconoció el mensaje, debemos revisar si ejecutó paso a paso la factorización, la conversión binaria y el conteo de bits verificables con nuestras funciones, o si identificó inmediatamente la cadena conocida del Mensaje de Arecibo y recitó su contenido de memoria. La prueba definitiva consiste en modificar algunos bits clave o entregarle un mensaje nuevo e inédito con una estructura similar que no exista en internet: si logra extraer los números y describir los patrones reales del nuevo arreglo, está descifrando; si repite la descripción histórica de Arecibo o falla al analizar el cambio, solo estaba reconociendo por memoria.

> [!note]
> ## 7. La revelación *(solo después del Momento 3)*
>
> **No ejecuten esta parte hasta tener escritos el inventario y la lista de convenciones.** Lo que sigue es el mensaje pintado por secciones y ampliado, tal como lo describieron quienes lo diseñaron en 1974. Sirve para comparar, no para copiar: lo que vale es lo que ustedes descifraron con código antes de verlo.
>
> La celda siguiente es una **CAJA**: `revelar(bits)` pinta el mensaje por secciones y `zoom(bits, seccion)` amplía una sección.

> [!code] Código
> ```python
> # CAJA: la revelación.  revelar(bits) muestra el mensaje coloreado por secciones;
> # zoom(bits, seccion) amplía una sección: "numeros", "elementos", "nucleotidos",
> # "helice", "humano", "sistema_solar" o "telescopio".
> import matplotlib.pyplot as plt
> import numpy as np
>
> _SECCIONES = {   # nombre: (fila_desde, fila_hasta, color, título)
>     "numeros":       (0, 4,   "#555555", "Números del 1 al 10"),
>     "elementos":     (4, 10,  "#7B3FB0", "Elementos: H, C, N, O, P"),
>     "nucleotidos":   (10, 30, "#2E8B57", "Fórmulas de las piezas del ADN"),
>     "helice":        (25, 46, "#2F6DB5", "Doble hélice del ADN"),
>     "humano":        (45, 55, "#C9573C", "Ser humano, estatura y población"),
>     "sistema_solar": (55, 60, "#D9A400", "Sistema solar"),
>     "telescopio":    (60, 73, "#8B3A62", "Radiotelescopio de Arecibo"),
> }
>
> def _matriz(bits):
>     return np.array([[int(bits[f * 23 + c]) for c in range(23)] for f in range(73)])
>
> def _seccion_de(f, c):
>     if f < 4: return "numeros"
>     if f < 10: return "elementos"
>     if f < 30 and not (f >= 25 and c in (10, 11)): return "nucleotidos"
>     if f < 46: return "helice"
>     if f < 55: return "humano"
>     if f < 60: return "sistema_solar"
>     return "telescopio"
>
> def _rgb(h):
>     return np.array([int(h[i:i + 2], 16) / 255 for i in (1, 3, 5)])
>
> def _numero(columna_bits):
>     v = 0
>     for b in columna_bits:
>         v = 2 * v + int(b)
>     return v
>
> def revelar(bits):
>     M = _matriz(bits)
>     img = np.ones((73, 23, 3))
>     for f in range(73):
>         for c in range(23):
>             if M[f, c]:
>                 img[f, c] = _rgb(_SECCIONES[_seccion_de(f, c)][2])
>     fig, ax = plt.subplots(figsize=(9, 12))
>     ax.imshow(img, interpolation="nearest")
>     ax.set_xticks([]); ax.set_yticks([])
>     for nombre, (f0, f1, color, titulo) in _SECCIONES.items():
>         y = {"helice": 37, "nucleotidos": 19.5, "humano": 50}.get(nombre, (f0 + f1 - 1) / 2)
>         ax.annotate(titulo, xy=(23.5, y), fontsize=12, color=color, va="center", annotation_clip=False)
>     ax.set_title("El mensaje de Arecibo (16 nov. 1974) · 1 679 bits = 73 × 23", loc="left")
>     plt.subplots_adjust(right=0.55)
>     plt.show()
>
> def _rejilla(ax, f0, f1, c0=0, c1=23):
>     ax.set_xticks(range(c1 - c0)); ax.set_xticklabels(range(c0, c1), fontsize=7)
>     ax.set_yticks(range(f1 - f0)); ax.set_yticklabels(range(f0, f1), fontsize=7)
>     ax.set_xticks(np.arange(-.5, c1 - c0, 1), minor=True); ax.set_yticks(np.arange(-.5, f1 - f0, 1), minor=True)
>     ax.grid(which="minor", color="#cccccc", lw=0.5); ax.tick_params(which="minor", length=0)
>
> def _pintar(ax, M, f0, f1, color, c0=0, c1=23, seccion=None):
>     img = np.ones((f1 - f0, c1 - c0, 3))
>     for f in range(f0, f1):
>         for c in range(c0, c1):
>             if M[f, c]:
>                 propio = seccion is None or _seccion_de(f, c) == seccion
>                 img[f - f0, c - c0] = _rgb(color) if propio else _rgb("#d0d0d0")
>     ax.imshow(img, interpolation="nearest")
>     _rejilla(ax, f0, f1, c0, c1)
>
> def zoom(bits, seccion):
>     M = _matriz(bits)
>     f0, f1, color, titulo = _SECCIONES[seccion]
>     if seccion == "nucleotidos":
>         return _zoom_nucleotidos(M, color)
>     fig, ax = plt.subplots(figsize=(10, max(2.5, 0.45 * (f1 - f0) + 1)))
>     _pintar(ax, M, f0, f1, color, seccion=seccion)
>     ax.set_title(titulo, loc="left", color=color, pad=40 if seccion in ("numeros", "elementos", "sistema_solar", "humano") else 8)
>     ax.set_ylim(f1 - f0 - 0.5, -0.5)
>     if seccion == "numeros":
>         for j in (0, 2, 4, 6, 8, 10, 12):
>             ax.text(j, -0.7, str(_numero(M[0:3, j])), ha="center", va="bottom", fontsize=11, fontweight="bold")
>         for izq, der in ((14, 15), (17, 18), (20, 21)):
>             ax.text(izq + 0.5, -0.7, str(_numero(list(M[0:3, der]) + list(M[0:3, izq]))), ha="center", va="bottom",
>                     fontsize=11, fontweight="bold")
>         ax.set_xlabel("Cada número se lee de arriba abajo; la fila 3 es la marca. El 8, el 9 y el 10 ocupan dos "
>                       "columnas: la de la derecha lleva los bits altos.", fontsize=9)
>     elif seccion == "elementos":
>         nombres = {1: "H", 6: "C", 7: "N", 8: "O", 15: "P"}
>         for j in range(9, 14):
>             z = _numero(M[5:9, j])
>             ax.text(j, -0.7, f"{z} {nombres.get(z, '?')}", ha="center", va="bottom", fontsize=9, fontweight="bold", rotation=90)
>         ax.set_xlabel("Números atómicos 1, 6, 7, 8 y 15: hidrógeno, carbono, nitrógeno, oxígeno y fósforo, "
>                       "los elementos del ADN.", fontsize=9)
>     elif seccion == "helice":
>         ax.set_xlabel("Las dos hebras retorcidas son la doble hélice. La barra del centro (columnas 10 y 11) es, "
>                       "según sus diseñadores,\nel número de nucleótidos del genoma humano escrito en binario. "
>                       "Termina justo en la cabeza del humano.", fontsize=9)
>     elif seccion == "humano":
>         ax.annotate("cabeza: un solo bit", xy=(10, 1), xytext=(13.5, 1.2), fontsize=9,
>                     arrowprops=dict(arrowstyle="->", color="#333"))
>         ax.text(2, -0.7, "estatura", ha="center", va="bottom", fontsize=9, fontweight="bold")
>         ax.text(19.5, -0.7, "población", ha="center", va="bottom", fontsize=9, fontweight="bold")
>         ax.set_xlabel("A la izquierda, una línea vertical con su número: la estatura. A la derecha, un número "
>                       "en binario: la población de la Tierra en 1974.", fontsize=9)
>     elif seccion == "sistema_solar":
>         etiquetas = {3: "Sol", 6: "Mer", 8: "Ven", 10: "Tierra", 12: "Mar", 14: "Júp", 16: "Sat",
>                      18: "Ura", 20: "Nep", 22: "Plu"}
>         for c, t in etiquetas.items():
>             ax.text(c, -0.7, t, ha="center", va="bottom", fontsize=8, rotation=60)
>         ax.set_xlabel("\nEl Sol y nueve planetas (en 1974 Plutón contaba como planeta). La Tierra está levantada: "
>                       "de ahí venimos.", fontsize=9)
>     elif seccion == "telescopio":
>         ax.set_xlabel("El radiotelescopio que mandó el mensaje, apuntando hacia abajo. La fila de abajo trae "
>                       "su diámetro en binario.", fontsize=9)
>     plt.tight_layout()
>     plt.show()
>
> def _zoom_nucleotidos(M, color):
>     nombres = [["desoxirribosa", "adenina", "timina", "desoxirribosa"],
>                ["fosfato", None, None, "fosfato"],
>                ["desoxirribosa", "citosina", "guanina", "desoxirribosa"],
>                ["fosfato", None, None, "fosfato"]]
>     formulas = {"desoxirribosa": "C₅H₇O", "adenina": "C₅H₄N₅", "timina": "C₅H₅N₂O₂", "fosfato": "PO₄",
>                 "citosina": "C₄H₄N₃O", "guanina": "C₅H₄N₅O"}
>     fig, ejes = plt.subplots(4, 4, figsize=(10, 10))
>     for i, f0 in enumerate((11, 16, 21, 26)):
>         for j, c0 in enumerate((0, 6, 12, 18)):
>             a = ejes[i][j]
>             if nombres[i][j] is None:
>                 a.axis("off"); continue
>             caja = M[f0:f0 + 4, c0:c0 + 5]
>             a.imshow(1 - caja, cmap="gray", vmin=0, vmax=1, interpolation="nearest")
>             a.set_xticks(range(5)); a.set_xticklabels(list("HCNOP")); a.xaxis.tick_top(); a.set_yticks([])
>             a.set_xticks(np.arange(-.5, 5, 1), minor=True); a.set_yticks(np.arange(-.5, 4, 1), minor=True)
>             a.grid(which="minor", color="#bbbbbb", lw=0.6); a.tick_params(which="minor", length=0)
>             a.axhline(2.5, color=color, lw=1.5, ls="--")
>             valores = [_numero(caja[0:3, c]) for c in range(5)]
>             n = nombres[i][j]
>             a.set_xlabel("  ".join(str(v) for v in valores) + f"\n{n}  {formulas[n]}", fontsize=9, color="#1d5c3a")
>     fig.suptitle("Fórmulas de las piezas del ADN: cada caja es una molécula.\n"
>                  "Cada columna, un número de 3 bits (la fila punteada es la marca); las columnas cuentan átomos de "
>                  "H, C, N, O y P.", fontsize=10, x=0.02, ha="left")
>     plt.tight_layout()
>     plt.show()
> ```

> [!code] Código
> ```python
> revelar(bits)
> ```

> [!question]
> Ahora, sección por sección. Para cada una, comparen con su inventario: ¿qué leyeron bien con código?, ¿qué adivinaron bien con los ojos?, ¿qué se les pasó?
>
> ✍️ *Comparación con el inventario:* Con código logramos descifrar con total precisión los números del 1 al 10 y los cinco números atómicos del ADN (H, C, N, O, P). Con los ojos adivinamos correctamente las siluetas principales (doble hélice, figura humana, sistema solar y radiotelescopio), mientras que se nos pasaron detalles codificados específicos dentro de los dibujos, como el número exacto de nucleótidos del genoma en la barra central del ADN, la estatura exacta, la población mundial de 1974 y el diámetro del telescopio.

> [!code] Código
> ```python
> zoom(bits, "numeros")
> ```

> [!code] Código
> ```python
> zoom(bits, "elementos")
> ```

> [!code] Código
> ```python
> zoom(bits, "nucleotidos")
> ```

> [!code] Código
> ```python
> zoom(bits, "helice")
> ```

> [!code] Código
> ```python
> zoom(bits, "humano")
> ```

> [!code] Código
> ```python
> zoom(bits, "sistema_solar")
> ```

> [!code] Código
> ```python
> zoom(bits, "telescopio")
> ```

> [!question]
> ✍️ **Después de la revelación** (tres preguntas, van al informe):
>
> 1. ¿Qué parte del mensaje habrían podido descifrar sin saber química? ¿Y sin saber que en 1974 Plutón contaba como planeta? Agreguen esas convenciones a su lista.
>    - Sin saber química habríamos podido descifrar la secuencia numérica inicial (1 al 10) y distinguir las siluetas visuales (la hélice, la figura humana, el sistema solar y el telescopio), pero las fórmulas de los nucleótidos habrían sido solo matrices de números sin sentido molecular. Sin saber que en 1974 Plutón era considerado un planeta, habríamos interpretado una estrella con nueve planetas en lugar de ocho, pudiendo confundir el sistema solar con otro sistema estelar.
>    - *Convenciones adicionales:* (a) La notación química organiza los conteos de átomos en el orden fijo `H, C, N, O, P` presentado en la sección anterior (deducible por correspondencia). (b) El sistema solar incluye nueve cuerpos orbitando el Sol según la clasificación astronómica vigente en 1974 (convención histórica/contextual).
> 2. La cabeza del humano es **un solo bit**, y la barra del ADN termina justo en ella. ¿Es casualidad o es un mensaje? ¿Cómo lo sabrían?
>    - No es casualidad: es un diseño intencional. La barra central del ADN representa la secuencia genética (el número de nucleótidos del genoma) y desemboca directamente en la cabeza del ser humano para comunicar la relación causal y biológica entre nuestra información genética y nuestra especie. Lo sabemos analizando la simetría espacial del diseño (columnas 10 y 11 alineadas con el eje central del cuerpo) y contrastándolo con la documentación de los diseñadores del mensaje (Frank Drake y Carl Sagan).
> 3. Si ustedes hubieran diseñado el mensaje, ¿qué habrían cambiado para que fuera más fácil de leer? (Lo van a poder hacer en la sesión 2.)
>    - Habríamos añadido un encabezado o leyenda inicial que ilustrara explícitamente la dirección de lectura binaria (por ejemplo, marcando el bit más significativo y el menos significativo), utilizado filas separadoras vacías más claras entre secciones temáticas y evitado mezclar datos numéricos en los bordes de los pictogramas (como en la figura humana o el telescopio) para no confundir medidas con partes del dibujo.

> [!note]
> ## Foto de las predicciones
>
> Suban la foto del papel de Momento 1 (en Colab: el ícono de carpeta a la izquierda → subir) y pongan aquí su nombre de archivo:
>
> `foto_predicciones_sesion1.jpg`

> [!note]
> ---
> ### Antes de irse
> 4. **Ejecuten todo de arriba a abajo** (*Entorno de ejecución → Reiniciar y ejecutar todo*). Si algo falla, no está terminado.
> 5. **Guarden una copia en su repositorio `intro-cc`:** *Archivo → Guardar una copia en GitHub*. Cada integrante en el suyo.
> 6. La **bitácora de IA** es individual y va aparte (la plantilla es `bitacora_IA_L1.md`, en la carpeta `introduccion_cc` del repositorio de laboratorios).
