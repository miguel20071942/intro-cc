> [!note]
> # L1 · Primer contacto — Sesión 2: **Responder**
> ### Introducción a las Ciencias de la Computación · UNAL Manizales
>
> En la sesión 1 recibieron un mensaje. Hoy escriben uno: su especie le va a hablar a otra especie del salón, que lo va a leer **sin instrucciones**, mientras el canal le daña bits al azar.
>
> Al final de la sesión viene **el día del contacto**: los mensajes se reparten y cada equipo intenta leer uno ajeno con las herramientas que construyó la semana pasada.

> [!code] Código
> ```python
> EQUIPO = ["Miguel Angel Marin", "Santiago Ocampo", "", ""]
> MI_BASE = 6
> NOMBRE_ESPECIE = "Kraxos"
> ```

> [!question]
> ## Momento 1 — Predecir *(en papel)*
>
> **P4.** ¿Qué fracción de su mensaje creen que la otra especie va a entender? Un número entre 0 y 1, y qué sección creen que se va a perder primero.
>
> **P5.** Si el canal daña 1 de cada 100 bits, ¿cuántos bits dañados esperan en su mensaje? ¿Y si lo mandan tres veces?
>
> ✍️
> - P4:La única sección universal es la Sección 1 (conteo en base 6), porque las matemáticas pueden deducirse sin contexto cultural. La primera sección en perderse será la Sección 2 ("UNAL"), ya que utiliza caracteres del alfabeto latino y una sigla en español sin ningún diccionario previo (para otra especie serán solo formas geométricas sin significado)
> - P5:En un solo envío ($817\text{ bits}$): Se esperan $817 \times \frac{1}{100} = \mathbf{8.17\text{ bits}}$ dañados en promedio (entre 8 y 9 bits alterados).Si se manda tres veces ($817 \times 3 = 2\,451\text{ bits}$): En toda la transmisión se dañarían $2\,451 \times \frac{1}{100} = \mathbf{24.51\text{ bits}}$ en total

> [!note]
> ## 0. Sus herramientas
>
> Peguen aquí **sus** funciones de la sesión 1 y de la tarea puente. Son suyas: se reutilizan, no se reescriben. Las pruebas de abajo verifican que llegaron completas.

> [!code] Código
> ```python
> # Peguen aquí: es_primo, rectangulos, filas_de, dibujar, leer_numero, columna,
> #              a_base_rec (o a_base_ciclo) y de_base
> def es_primo(n):
>     es_primo = n >= 2
>     d = 2
>
>     while d * d <= n and es_primo:
>         if n % d == 0:
>             es_primo = False
>         d = d + 1
>     return es_primo
>
> def rectangulos(n):
>     """CONTRATO
>     Recibe: un entero n >= 1.
>     Devuelve: la lista de parejas (filas, columnas) con filas * columnas == n,
>               ordenada de menor a mayor número de filas.
>     """
>     # TU CÓDIGO AQUÍ
>     resultado = []
>     for filas in range(1, n + 1):
>         if n % filas == 0:
>             columnas = n // filas
>             resultado.append((filas, columnas))
>     return resultado
>     pass
>
> def filas_de(bits, columnas):
>     """CONTRATO
>     Recibe: una cadena de bits y un número de columnas que divide su longitud.
>     Devuelve: la lista de renglones, cada uno de longitud 'columnas'.
>     """
>     # TU CÓDIGO AQUÍ
>     if len(bits) % columnas != 0:
>         return []
>     else:
>         resultado = []
>         for i in range(0, len(bits), columnas):
>             resultado.append(bits[i:i + columnas])
>         return resultado
>
> def dibujar(bits, columnas):
>     """CONTRATO
>     Recibe: una cadena de bits y un número de columnas.
>     Devuelve: nada. Imprime los renglones con '█' para 1 y ' ' para 0.
>     """
>     # TU CÓDIGO AQUÍ (usen filas_de)
>     renglones = filas_de(bits, columnas)
>     for r in renglones:
>         linea = r.replace("1", "█").replace("0", " ")
>         print(linea)
>     pass
>
> def leer_numero(lista_bits):
>     """CONTRATO
>     Recibe: una lista de enteros 0 y 1; el primero es el más significativo.
>     Devuelve: el entero que representan en base 2.
>     """
>     # TU CÓDIGO AQUÍ
>     suma = 0
>     for bit in lista_bits:
>         suma = (suma * 2) + bit
>     return suma
>
> def columna(bits, columnas, j, fila_desde, fila_hasta):
>     """CONTRATO
>     Recibe: los bits, el ancho de la cuadrícula, una columna j y un rango de filas.
>     Devuelve: la lista de enteros (0/1) de la columna j, desde fila_desde hasta fila_hasta - 1,
>               de arriba hacia abajo.
>     """
>     # TU CÓDIGO AQUÍ
>     resultado = []
>     for f in range(fila_desde, fila_hasta):
>         indice = f * columnas + j
>         resultado.append(int(bits[indice]))
>     return resultado
>
> def a_base_rec(n, b):
>     """Mismo contrato, escrita de forma recursiva."""
>     # TU CÓDIGO AQUÍ
>     if n < b:
>         return [n]
>     return a_base_rec(n // b, b) + [n % b]
>     pass
>
>
> def de_base(digitos, b):
>     """CONTRATO
>     Recibe: una lista de dígitos en base b, el más significativo primero.
>     Devuelve: el entero que representan.
>     """
>     # TU CÓDIGO AQUÍ
>     numero = 0
>     for d in digitos:
>         numero = numero * b + d
>     return numero
>     pass
> ```

> [!code] Código
> ```python
> # Pruebas de que las herramientas llegaron completas
> assert es_primo(97) and not es_primo(1679)
> assert rectangulos(12) == [(1, 12), (2, 6), (3, 4), (4, 3), (6, 2), (12, 1)]
> assert filas_de("101100", 3) == ["101", "100"]
> assert leer_numero([1, 0, 1, 1]) == 11
> assert a_base_rec(1974, 6) == [1, 3, 0, 5, 0] and de_base([1, 3, 0, 5, 0], 6) == 1974
> assert MI_BASE in (2, 3, 5, 6, 8, 12), "Escriban la base que les tocó."
> print("✔ Herramientas listas. Especie de base", MI_BASE)
> ```

> [!question]
> ## 1. ¿Cuántos bits por dígito?
>
> Si cuentan en base 6, sus dígitos van de 0 a 5, y cada uno hay que escribirlo con bits. ¿Con cuántos? Es la desigualdad de la clase 3: con $k$ bits se escriben $2^k$ valores distintos, así que hace falta el menor $k$ con $2^k \geq b$.
>
> - `bits_por_digito(b)` devuelve ese $k$.
> - `digito_a_bits(d, ancho)` escribe un dígito con exactamente `ancho` bits, rellenando con ceros a la izquierda. Usen su `a_base_rec(d, 2)`.

> [!code] Código
> ```python
> def bits_por_digito(b):
>     """CONTRATO. Recibe una base b >= 2. Devuelve el menor k con 2**k >= b."""
>     k = 1
>     while 2 ** k < b:
>         k = k + 1
>     return k
>
>
> def digito_a_bits(d, ancho):
>     """CONTRATO. Recibe un entero 0 <= d < 2**ancho. Devuelve una cadena de exactamente
>     'ancho' caracteres '0'/'1' con d en binario, rellenada con ceros a la izquierda."""
>     bits_lista = a_base_rec(d, 2)
>     cadena = ""
>     for bit in bits_lista:
>         cadena = cadena + str(bit)
>     ceros_faltantes = ancho - len(cadena)
>     return ("0" * ceros_faltantes) + cadena
> ```

> [!code] Código
> ```python
> # Pruebas
> assert [bits_por_digito(b) for b in (2, 3, 5, 6, 8, 12)] == [1, 2, 3, 3, 3, 4]
> assert digito_a_bits(5, 3) == "101" and digito_a_bits(1, 4) == "0001" and digito_a_bits(0, 2) == "00"
> ANCHO = bits_por_digito(MI_BASE)
> print("✔ Su especie escribe cada dígito con", ANCHO, "bits.")
> ```

> [!question]
> ## 2. El mensaje, por secciones
>
> **La representación del mensaje.** Un mensaje es una **lista de renglones**, y cada renglón es una cadena de `'0'` y `'1'` de exactamente `COLUMNAS` caracteres. Elijan `COLUMNAS` pensando en la pregunta de Arecibo: ¿cómo va a adivinar la otra especie el ancho del rectángulo?
>
> Cada sección del mensaje es una **función** que devuelve una lista de renglones. Al final, `ensamblar` las pone una debajo de la otra. Si mañana quieren cambiar una sección, cambian una función y nada más: eso es pensar en módulos.
>
> ### La sección obligatoria: contar
>
> `codificar_conteo(hasta)` escribe los números del 1 al `hasta` **en su base**, uno por renglón, cada dígito con `ANCHO` bits, alineados a la izquierda y rellenando el renglón con ceros. Ojo: deben contar al menos hasta un poco más allá de su base, para que se vea el momento en que aparece el "10".

> [!code] Código
> ```python
> COLUMNAS = 19    # Número primo para que la otra especie deduzca el ancho del rectángulo
>
>
> def renglon(contenido):
>     """CONTRATO. Recibe una cadena de bits de longitud <= COLUMNAS.
>     Devuelve esa cadena rellenada con ceros a la derecha hasta COLUMNAS caracteres."""
>     assert len(contenido) <= COLUMNAS, f"El renglón no cabe: {len(contenido)} > {COLUMNAS}"
>     return contenido + "0" * (COLUMNAS - len(contenido))
>
>
> def codificar_conteo(hasta):
>     """CONTRATO. Devuelve una lista de renglones: el renglón i tiene el número i
>     (de 1 a 'hasta') escrito en MI_BASE, cada dígito con ANCHO bits."""
>     lista_renglones = []
>     for i in range(1, hasta + 1):
>         digitos = a_base_rec(i, MI_BASE)
>         bits_numero = ""
>         for d in digitos:
>             bits_numero = bits_numero + digito_a_bits(d, ANCHO)
>         lista_renglones.append(renglon(bits_numero))
>     return lista_renglones
> ```

> [!code] Código
> ```python
> # Pruebas
> conteo = codificar_conteo(MI_BASE + 2)
> assert len(conteo) == MI_BASE + 2 and all(len(r) == COLUMNAS for r in conteo)
> assert conteo[0].startswith(digito_a_bits(1, ANCHO))
> assert conteo[MI_BASE - 1].startswith(digito_a_bits(1, ANCHO) + digito_a_bits(0, ANCHO)), "El renglón de MI_BASE debe decir '10'."
> print("✔ El conteo delata su base. Así se ve:")
> dibujar("".join(conteo), COLUMNAS)
> ```

> [!note]
> ### Sus secciones
>
> Una función por sección, cada una con su contrato. Copien la plantilla tantas veces como secciones tengan.

> [!code] Código
> ```python
> def codificar_seccion_2():
>     """CONTRATO. Qué dice: sigla de la Universidad Nacional de Colombia (UNAL).
>     Devuelve: lista de 6 renglones de COLUMNAS bits (1 separador + 5 de texto)."""
>     return [
>         renglon("0000000000000000000"),
>         renglon("0101010001001001000"),
>         renglon("0101011001010101000"),
>         renglon("0101010101011101000"),
>         renglon("0101010011010101000"),
>         renglon("0111010001010101110"),
>     ]
>
>
> def codificar_seccion_3():
>     """CONTRATO. Qué dice: siluetas de la Torre al Cielo de Chipre (arriba)
>     y la Torre de El Cable de Manizales (abajo).
>     Devuelve: lista de 15 renglones de COLUMNAS bits."""
>     return [
>         renglon("0000000000000000000"),
>         renglon("0000000001000000000"),  # Torre al Cielo de Chipre
>         renglon("0000000111110000000"),
>         renglon("0000001101011000000"),
>         renglon("0000000111110000000"),
>         renglon("0000000011100000000"),
>         renglon("0000000011100000000"),
>         renglon("0000000111110000000"),
>         renglon("0000000000000000000"),
>         renglon("0000000011100000000"),  # Torre de El Cable
>         renglon("0000000101010000000"),
>         renglon("0000001011101000000"),
>         renglon("0000010101010100000"),
>         renglon("0000111111111110000"),
>         renglon("0000000000000000000"),
>     ]
>
>
> def codificar_seccion_4():
>     """CONTRATO. Qué dice: íconos de Manizales — Catedral Basílica (arriba)
>     y Estadio Palogrande (abajo).
>     Devuelve: lista de 14 renglones de COLUMNAS bits."""
>     return [
>         renglon("0000000001000000000"),  # Catedral Basílica
>         renglon("0000000011100000000"),
>         renglon("0000010011100100000"),
>         renglon("0000111011101110000"),
>         renglon("0001111111111111000"),
>         renglon("0001101110111011000"),
>         renglon("0011111111111111100"),
>         renglon("0000000000000000000"),
>         renglon("0000111111111110000"),  # Estadio Palogrande
>         renglon("0011100001000011100"),
>         renglon("0111000010100001110"),
>         renglon("0011100001000011100"),
>         renglon("0000111111111110000"),
>         renglon("0000000000000000000"),
>     ]
> ```

> [!code] Código
> ```python
> def ensamblar(secciones):
>     """CONTRATO. Recibe una lista de secciones (cada una, una lista de renglones).
>     Devuelve una sola cadena de bits: todos los renglones, uno detrás de otro."""
>     resultado = ""
>     for seccion in secciones:
>         for fila in seccion:
>             resultado = resultado + fila
>     return resultado
>
>
> mensaje = ensamblar([
>     codificar_conteo(MI_BASE + 2),
>     codificar_seccion_2(),
>     codificar_seccion_3(),
>     codificar_seccion_4()
> ])
> print("Longitud del mensaje:", len(mensaje), "bits")
> print("Formas de dibujarlo:", rectangulos(len(mensaje)))
> dibujar(mensaje, COLUMNAS)
> ```

> [!question]
> ### La forma
>
> ¿Su mensaje tiene una longitud que obligue a **una sola** manera de dibujarlo, como Arecibo? Si no, agreguen renglones de relleno hasta que el número de renglones lo permita. Pista: si `COLUMNAS` es primo y el número de renglones también, solo hay dos rectángulos posibles (y uno es el traspuesto del otro). Escriban `es_semiprimo(n)` con su `rectangulos` si les sirve.

> [!code] Código
> ```python
> # Ajusten aquí la forma del mensaje (relleno) y vuelvan a ensamblar si hace falta
> def es_semiprimo(n):
>     for f, c in rectangulos(n):
>         if es_primo(f) and es_primo(c):
>             return True
>     return False
>
> print("Longitud:", len(mensaje), "bits")
> print("¿Es semiprimo?:", es_semiprimo(len(mensaje)))
> print("Rectángulos posibles:", rectangulos(len(mensaje)))
> ```

> [!question]
> ## 3. Las preguntas incómodas de la representación
>
> Su mensaje tiene que responder **al menos dos** de estas. En cada una, escriban cómo lo resolvieron (o por qué no se puede).
>
> 1. **Ancho fijo.** Si cada número va en un renglón de `COLUMNAS` bits, ¿cuál es el número más grande que cabe en su base? ¿Qué pasa si quieren decir cuántos habitantes tiene Manizales? A esto se le llama **desbordamiento**.
> 2. **Decimales.** ¿Cómo dicen la altura de Manizales en metros si el otro no sabe qué es un metro, ni dónde va la coma? *(Pista real: en 1972, las placas de las sondas Pioneer usaron una propiedad del átomo de hidrógeno como regla para medir, porque el hidrógeno es igual en todo el universo.)*
> 3. **Texto.** ¿Por qué no pueden mandar la palabra "Manizales" en ASCII o en Unicode? ¿Qué **sí** pueden mandar?
> 4. **Forma.** ¿Cómo va a saber la otra especie cuántas columnas tiene su mensaje?

> [!code] Código
> ```python
> # Si alguna respuesta necesita cuentas, háganlas aquí.
> # Por ejemplo, el número más grande que cabe en un renglón:
> # digitos_por_renglon = COLUMNAS // ANCHO
> # maximo = MI_BASE ** digitos_por_renglon - 1
>
> digitos_por_renglon = COLUMNAS // ANCHO
> maximo = MI_BASE ** digitos_por_renglon - 1
>
> poblacion_manizales = 455000
> digitos_poblacion = len(a_base_rec(poblacion_manizales, MI_BASE))
> bits_poblacion = digitos_poblacion * ANCHO
>
> print(f"Dígitos por renglón: {digitos_por_renglon} (usan {digitos_por_renglon * ANCHO} de {COLUMNAS} bits)")
> print(f"Número más grande que cabe en un renglón: {maximo} (555555 en base 6)")
> print(f"Población de Manizales ({poblacion_manizales}) requiere {digitos_poblacion} dígitos = {bits_poblacion} bits -> ¡Desbordamiento!")
> ```

> [!question]
> ✍️
> 1. Con 19 columnas (3 bits/dígito) caben máximo 6 dígitos en base 6 ($555555_6 = 46\,655$). Los $455\,000$ habitantes de Manizales requieren 8 dígitos (24 bits), lo que produce desbordamiento (no cabe en un renglón).
> 2. No se puede enviar "2150.5 m" porque el metro y la coma decimal son convenciones humanas sin significado universal.Se resolvería contando múltiplos enteros de una constante física (como la longitud de onda del hidrógeno).
> 3. No enviamos "Manizales" en ASCII/Unicode porque sin un diccionario previo solo serían números arbitrarios. En su lugar mandamos siluetas visuales en mapa de bits: la sigla UNAL y los monumentos de la ciudad (Torres de Chipre y El Cable, Catedral y Estadio).
> 4. Diseñamos el mensaje con 817 bits, un semiprimo producto de dos primos ($43 \times 19$). Al factorizarlo, el receptor solo tiene dos rectángulos posibles ($43 \times 19$ o $19 \times 43$) y descubre de inmediato el ancho de 19 columnas.

> [!note]
> ## 4. El canal
>
> Entre las estrellas el mensaje se daña: la radiación voltea bits. Vamos a simular ese canal y a comparar **dos maneras** de proteger el mensaje.
>
> - `ruido(bits, p)`: voltea cada bit con probabilidad `p` (usen `random.random() < p`).
> - **Manera 1, paridad.** `con_paridad(bits, ancho)` agrega a cada renglón un bit extra para que el número de unos del renglón sea **par**. Del otro lado, `filas_danadas(bits, ancho)` dice qué renglones llegaron con número impar de unos. (Ojo: el ancho de cada renglón ahora es `ancho + 1`.)
> - **Manera 2, repetición.** `repetir3(bits)` manda cada bit tres veces seguidas; `votar(bits)` toma cada grupo de tres y se queda con el que más aparece.

> [!code] Código
> ```python
> import random
>
>
> def ruido(bits, p):
>     """CONTRATO. Devuelve una copia de bits donde cada bit se volteó con probabilidad p."""
>     resultado = ""
>     for b in bits:
>         if random.random() < p:
>             resultado += "1" if b == "0" else "0"
>         else:
>             resultado += b
>     return resultado
>
>
> def con_paridad(bits, ancho):
>     """CONTRATO. Parte bits en renglones de 'ancho' y a cada uno le agrega un bit de paridad
>     al final, de modo que cada renglón (ahora de ancho + 1) tenga un número par de unos."""
>     resultado = ""
>     for fila in filas_de(bits, ancho):
>         bit_paridad = "1" if fila.count("1") % 2 == 1 else "0"
>         resultado += fila + bit_paridad
>     return resultado
>
>
> def filas_danadas(bits, ancho):
>     """CONTRATO. Recibe bits con paridad, en renglones de ancho + 1.
>     Devuelve la lista de índices de los renglones con un número impar de unos."""
>     danadas = []
>     renglones = filas_de(bits, ancho + 1)
>     for i in range(len(renglones)):
>         if renglones[i].count("1") % 2 == 1:
>             danadas.append(i)
>     return danadas
>
>
> def repetir3(bits):
>     """CONTRATO. Devuelve bits con cada carácter repetido tres veces seguidas."""
>     resultado = ""
>     for b in bits:
>         resultado += b * 3
>     return resultado
>
>
> def votar(bits):
>     """CONTRATO. Recibe una cadena de longitud múltiplo de 3. Por cada grupo de tres,
>     devuelve el bit que aparece al menos dos veces."""
>     resultado = ""
>     for i in range(0, len(bits), 3):
>         grupo = bits[i:i + 3]
>         resultado += "1" if grupo.count("1") >= 2 else "0"
>     return resultado
> ```

> [!code] Código
> ```python
> # Pruebas
> random.seed(0)
> assert ruido("0101", 0) == "0101" and ruido("0101", 1) == "1010"
> assert con_paridad("110100", 3) == "11001001"
> assert filas_danadas("11001001", 3) == [] and filas_danadas("11011001", 3) == [0]
> assert repetir3("10") == "111000" and votar("110001") == "10" and votar(repetir3("1011")) == "1011"
> print("✔ El canal y las dos protecciones pasan las pruebas.")
> ```

> [!question]
> ### Medir, no suponer
>
> Corran el canal **mil veces** con `p = 0.01` sobre su mensaje y cuenten:
>
> - **Paridad:** ¿en cuántas transmisiones hubo al menos un error? ¿En cuántas `filas_danadas` lo detectó? ¿En cuántas hubo errores que **no** detectó? (¿Cómo puede pasar eso?)
> - **Repetición:** ¿en cuántas transmisiones `votar` recuperó el mensaje **exacto**?
> - ¿Cuántos bits cuesta cada manera, comparado con el mensaje original?

> [!code] Código
> ```python
> N = 1000
> p = 0.01
>
> con_error = detectadas = no_detectadas = 0
> recuperadas_rep = 0
>
> for _ in range(N):
>     # Paridad
>     protegido = con_paridad(mensaje, COLUMNAS)
>     recibido = ruido(protegido, p)
>     if recibido != protegido:
>         con_error += 1
>         if len(filas_danadas(recibido, COLUMNAS)) > 0:
>             detectadas += 1
>         else:
>             no_detectadas += 1
>
>     # Repetición
>     protegido_rep = repetir3(mensaje)
>     recibido_rep = ruido(protegido_rep, p)
>     if votar(recibido_rep) == mensaje:
>         recuperadas_rep += 1
>
> print("Paridad   : con error", con_error, "| detectadas", detectadas, "| sin detectar", no_detectadas)
> print("Repetición: recuperadas exactas", recuperadas_rep, "de", N)
> print("Costo en bits: original", len(mensaje), "| paridad", len(con_paridad(mensaje, COLUMNAS)),
>       "| repetición", len(repetir3(mensaje)))
> ```

> [!question]
> ✍️ **¿Cuál de las dos protecciones elegirían para un mensaje que tarda 25 000 años en llegar, y por qué?** Con sus números. Piensen en la diferencia entre **detectar** un error y **corregirlo**.
>
> R/: Aunque la paridad cuesta muy poco espacio (860 bits frente a los 817 bits originales, apenas $+43\text{ bits}$), en nuestra simulación 999 de 1000 envíos sufrieron errores; aunque detectó los 999, en todos esos casos el receptor habría recibido un mensaje roto sin poder repararlo (solo 1 de 1000 llegó intacto). Con repetición, aunque el mensaje cuesta el triple (2451 bits), se logra corregir el ruido y recuperar el mensaje exacto en 769 de 1000 transmisiones, elevando la probabilidad de éxito del $0.1\%$ al $76.9\%$.

> [!note]
> ## 5. Mandar el mensaje
>
> Elijan su protección, pasen el mensaje por el canal **una sola vez** con `p = 0.01` y guárdenlo en `mensaje.txt`: una sola línea de ceros y unos, **sin** decir cuántas columnas tiene ni qué protección usaron. Entréguenle el archivo al monitor. Súbanlo también a su repositorio.

> [!code] Código
> ```python
> PROTECCION = "repeticion"    # o "repeticion"
>
> if PROTECCION == "paridad":
>   enviado = ruido(con_paridad(mensaje, COLUMNAS), 0.01)
> else:
>   enviado = ruido(repetir3(mensaje), 0.01)
>
> with open("mensaje.txt", "w") as f:
>   f.write(enviado)
> print("mensaje.txt listo:", len(enviado), "bits. Entréguenlo al monitor.")
> ```

> [!code] Código
> ```python
> with open("mensaje.txt") as f:
>     recibido = f.read()
>
> recuperado = votar(recibido)
> dibujar(recuperado, 19)
> ```

> [!note]
> ## 6. El día del contacto
>
> El monitor les entrega el `mensaje.txt` de **otra especie**. Tienen 30 minutos para leerlo **con sus herramientas de la sesión 1**: `len`, `rectangulos`, `dibujar`, `columna`, `leer_numero`, `de_base`. Si sirven para un mensaje que nunca habían visto, eran herramientas de verdad; si solo servían para Arecibo, eran un truco.
>
> Súbanlo a Colab (ícono de carpeta → subir) con el nombre `recibido.txt`.

> [!code] Código
> ```python
> with open("recibido.txt") as f:
>     ajeno = f.read().strip()
>
> print("1. Longitud recibida:", len(ajeno), "bits")
> print("2. Formas posibles (con paridad):", rectangulos(len(ajeno)))
>
> # Detectamos filas dañadas por paridad (ancho útil = 19, ancho con paridad = 20)
> ANCHO_AJENO = 19
> print("3. Filas con error detectado por paridad:", filas_danadas(ajeno, ANCHO_AJENO))
>
> # Quitamos el bit de paridad (último bit de cada fila de 20) para recuperar las 19 columnas útiles
> filas_con_par = filas_de(ajeno, ANCHO_AJENO + 1)
> limpio = ""
> for r in filas_con_par:
>     limpio += r[:-1]
>
> print("\n4. Dibujo limpio (37 filas x 19 columnas):")
> dibujar(limpio, ANCHO_AJENO)
>
> # Usamos filas_de, leer_numero y de_base para leer los primeros renglones
> renglones = filas_de(limpio, ANCHO_AJENO)
> print("\n5. Lectura de los primeros 10 renglones (probando bloques de 3 bits):")
> for i in range(10):
>     fila = renglones[i]
>     digitos = []
>     for pos in range(0, 6, 3):
>         bits_digito = [int(b) for b in fila[pos:pos + 3]]
>         digitos.append(leer_numero(bits_digito))
>     print(f"Fila {i + 1:2d}: {fila[:6]} -> dígitos {digitos}")
>
> # Verificación con columna
> col_0 = columna(limpio, ANCHO_AJENO, 0, 0, 10)
> print("\n6. Columna 0 (filas 0..9):", col_0, "-> binario:", leer_numero(col_0))
> ```

> [!question]
> ✍️ **Lo que entendimos del mensaje de la especie ________ :**
>
> - **Ancho que dedujimos:** **19** columnas (20 recibidas con paridad), porque 740 bits se factoriza como $37 \times 20$, donde 37 es primo y $19 + 1$ corresponde a 19 columnas útiles (primo) más 1 bit de paridad por fila.
> - **Base que dedujimos:** **Base 6**, porque cuentan del 1 al 5 en bloques de 3 bits y en la fila 6 aparece el salto a dos dígitos (`001 000` = $10_6$).
> - **Protección que creemos que usaron:** **Paridad (`con_paridad`)**; detectamos 4 filas dañadas (`[22, 32, 33, 36]`).
> - **Lo que dice cada sección:**
>   1. Conteo numérico del 1 al 8 ($12_6$) en base 6.
>   2. Secuencia de números adicionales en base 6 (2, 3, 4, 7, 7).
>   3. Escudo del Once Caldas.

> [!question]
> ## 7. La revelación
>
> Cada especie lee en voz alta lo que entendió del mensaje que recibió, y la especie emisora revela su contrato de lectura. Anoten **de su propio mensaje**:
>
> | Paso de nuestro contrato de lectura | ¿Lo dio la otra especie? | Si no, ¿por qué? |
> |---|---|---|
> | | | |
> | | | |
> | | | |
>
> ¿Hubo algo que la otra especie entendió **mal**, con toda seguridad? (Recuerden la palabra "arma" en *La llegada*.)

> [!question]
> ## Momento 3 — Explicar *(una página por equipo)*
>
> 4. **Confronten P4** con lo que de verdad se entendió de su mensaje. ¿Por qué la diferencia?
> 5. **La convención escondida:** ¿qué supusieron **sin darse cuenta** que la otra especie no tenía?
> 6. **Paridad contra repetición:** cuál eligieron y por qué, con las mediciones de la sección 4.
> 7. Una frase que empiece por *"Representar es..."*. Compárenla con la de la sesión 1: ¿cambió?
>
> ✍️

> [!question]
> ## La IA como objeto de estudio *(para la bitácora individual)*
>
> Denle a su asistente el `mensaje.txt` de **otra especie** del salón (no el suyo), sin decirle nada más, y pídanle que lo traduzca. Ese mensaje no está en ningún lugar de internet: no lo puede reconocer, solo lo puede descifrar.
>
> Comparen lo que dice el asistente con lo que reveló la especie emisora. ¿Tradujo con seguridad algo que no estaba ahí? Esa es la escena del "arma", y la acaban de medir. Y vuelvan a la pregunta de la sesión 1: ¿qué hizo con Arecibo, descifrar o reconocer?

> [!note]
> ## Foto de las predicciones
>
> `foto_predicciones_sesion2.jpg`

> [!note]
> ---
> ### Antes de irse
> 8. **Ejecuten todo de arriba a abajo** (*Entorno de ejecución → Reiniciar y ejecutar todo*). Si algo falla, no está terminado.
> 9. **Guarden una copia en su repositorio `intro-cc`:** *Archivo → Guardar una copia en GitHub*. Cada integrante en el suyo.
> 10. La **bitácora de IA** es individual y va aparte (la plantilla es `bitacora_IA_L1.md`, en la carpeta `introduccion_cc` del repositorio de laboratorios).