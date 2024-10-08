# Problema

Javier está pasando un curso de Diseño y Análisis de Algoritmos. Este año, por primera vez en la historia, los profesores han decidido evaluar el curso mediante un examen final y Javier se ha dado cuenta de que, a grandes rasgos, está frito. A pesar de ser un poco barco, es de hecho un muchacho inteligente y rápidamente se da cuenta de que su única forma de aprobar era hacer trampa. El día de la prueba, Javier se sentó en el asiento que estaba entre Hansel y Elena para fijarse, con la esperanza de que, uniendo las preguntas respondidas por cada uno, se pudiera formar un examen correcto.

El examen tiene $n$ preguntas, ordenadas en la hoja. Elena y Hansel pueden no ser capaces de responder cada uno el examen entero, pero todas las preguntas que responden están correctas. Se conoce cuáles preguntas respondió cada uno y se reciben como dos listas ordenadas de enteros entre $1$ y $n$. Javier tiene $p$ oportunidades para mirar hacia la izquierda (hoja de Hansel) o hacia la derecha (hoja de Elena) y su agilidad mental le alcanza para ver las respuestas de $k$ preguntas consecutivas (en cualquier posición) cada vez que echa una mirada a un examen.

Ayude a Javier a saber la cantidad máxima de preguntas que puede responder con su (tramposa) estrategia.

## Abstracción

Sean los valores $a_i$ y $b_i$ con $i\isin [1,n]$ tal que:

$$
a_i =
\begin{cases}
1 & \text{si Elena tiene la respuesta de la pregunta i} \\
0 & \text{si no la tiene}\\
\end{cases}
$$

$$
b_i =
\begin{cases}
1 & \text{si Hansel tiene la respuesta de la pregunta i} \\
0 & \text{si no la tiene}\\
\end{cases}
$$

Denominemos una observación $q_{iP}\isin 2^\N$ al conjunto de los índices de las preguntas respondidas por la persona P desde la posición $i$ hasta la posición $i+k-1$. Diremos que una observación es hacia A si es hacia Elena y es hacia B si es hacia Hansel.

Sea S un conjunto de observaciones, entonces llamaremos costo de S a la función $c(S)=|\bigcup S|$

El problema consiste en encontrar $max$ $c(S)$ $s.a.$ $|S|=p$

## Solución Naive

Existen $2n$ posibles observaciones y queremos exactamente $p$ de estas. Por lo que el total de casos validos seria $
\begin{pmatrix}
2n \\
p
\end{pmatrix}
$. Por cada combinación, por cada oportunidad se deben tener en cuenta los posibles $k$ problemas que contiene. Por lo que la complejidad final sería $
\begin{pmatrix}
2n \\
p
\end{pmatrix}
pk$.

Es evidente que al explorar todas las combinaciones de tamaño $p$, se obtendrá la respuesta correcta.
<!-- Sea $S$ la respuesta correcta, esta tiene que tener una combinación de observaciones. -->

## Solución propuesta: Programación dinámica

El algoritmo consiste en computar cuál es la mayor cantidad de problemas que se pueden resolver partiendo de la posición $i$ (con $1\leq i\leq n$), contando con $p_i$ (con $1\leq p_i\leq p$) oportunidades en cada uno de los posibles escenarios. Cada escenario consiste en ver cuáles de los siguientes $k-1$ ejercicios son observados desde $A$ o $B$ respectivamente a partir de la posición $i$ por una ventana que comience antes de dicha posición. En otras palabras, si durante una oportunidad se observó a partir de un valor anterior a $i$, es posible que las primeras posiciones del intervalo sean observadas por un intervalo anterior. Por lo que se analizan para $k_A$ y $k_B$ $(0\leq k_A,k_B\leq k)$ posibles siguientes posiciones vistas desde una ventana previa.

Para un estado $(i,k_A,k_B,p_i)$ su valor depende de los estados:

- i. $(i+1,max(k_A-1,0),max(k_B-1,0),p_i)$
- ii. $(i+1,k-1,max(k_B-1,0),p_i-1)$
- iii. $(i+1,max(k_A-1,0),k-1,p_i-1)$
- iv. $(i+1,k-1,k-1,p_i-2)$

Que representan (i) no tomar ninguna oportunidad que parta de $i$, (ii) tomar una oportunidad que empiece en $i$ desde $A$, (iii) tomar una oportunidad que empiece en $i$ desde $B$ y (iv) tomar las oportunidades que empiecen en $i$ tanto en $A$ como en $B$.

Además, si hay una respuesta en la posición i, es posible ver dicha respuesta. Para esto hay diferentes escenarios:

1. Si $a_i=1$ y $k_A>0$ (el ejercicio es visto desde una oportunidad previa en A)
2. Si $b_i=1$ y $k_B>0$ (el ejercicio es visto desde una oportunidad previa en B)
3. Si $a_i=1$ y se decide hacer (ii) y (iv) (se decide hacer una oportunidad que empiece en A)
4. Si $b_i=1$ y se decide hacer (iii) y (iv) (se decide hacer una oportunidad que empiece en B)

Si ocurre uno de estos escenarios la solución debe aumentar en 1. Para más formalidad sean $r_A$ y $r_B$ tal que:

$
r_A =
\begin{cases}
1 & \text{si } k_A>0\\
0 & \text{en otro caso}\\
\end{cases}
$

$
r_B =
\begin{cases}
1 & \text{si } k_B>0\\
0 & \text{en otro caso}\\
\end{cases}
$

Entonces finalmente el costo quedaría:

$
c(i,k_A,k_B,p) =
\begin{cases}
c(\text{i})+max(r_Aa_i,r_Bb_i)\\
c(\text{ii})+max(a_i,r_Bb_i)\\
c(\text{iii})+max(r_Aa_i,b_i)\\
c(\text{iv})+max(a_i,b_i)\\
\end{cases}
$

Nota: $c(i,k_A,k_B,p_i)=0$ si $i>n$ y $c(i,k_A,k_B,p_i)=-1$ si  $p_i<0$

La respuesta del ejercicio sería $c(0,0,0,p)$

### Correctitud

Sea $S=\{q_{i_1C_1},q_{i_2C_2},...\}$ una solución, entonces existe una secuencia válida de valores $\=S$ conformado por ((i),(ii),(iii),(iv)) que parten de $c(0,0,0,p)$ en la cual se observa $S$ y sus valores son iguales. Diremos que $\=S \rightarrow S$ si esto ocurre.

Decimos que una solución $S$ es válida si $|S|\leq p$. Y una secuencia $\=S$ es válida si $ii(\=S) + iii(\=S) + 2 \cdot iv(\=S) \leq p$.

Si $n=1$, existen 4 posibles valores de $S$: $\{\}$, $\{q_{1A}\}$, $\{q_{1B}\}$, $\{q_{1A},q_{1B}\}$, y trivialmente a cada una se le corresponde la secuencias $\{$(i)$\}$,$\{$(ii)$\}$,$\{$(iii)$\}$, $\{$(iv)$\}$. Entonces, si $S$ es una solución válida para $p$, $\=S$ también es una secuencia válida y el costo asociado a $\=S$ es igual a $c(S)$.

Supongamos que $\exist N $ $\forall j, \forall S=\{q_{i_1C_1}, q_{i_2C_2,...}\}: ((q_{jA} \isin S$ $\vee$ $q_{jB} \isin S) => j<N) => \exist \=S : \=S\rightarrow S$.

En otras palabras, existe N tal que cualquier solución S sobre N ejercicios tiene $\=S$ tal que  $\=S\rightarrow S$

Dado $S$ una solución válida de un problema con $N+1$ ejercicios, sea $S'=S-\{q_{(N+1)A}, q_{(N+1)B}\}$. Luego, $S'$ es una solución válida de un problema de tamaño $N$. Por lo que existe $\=S'$ tal que $\=S'\rightarrow S'$.

Luego trivialmente se comprueba para cada uno de los siguientes casos:

1. Si $q_{(N+1)A}\not\isin S$ y $q_{1B}\not\isin S$ entonces $\=S=\=S'+$(i)
1. Si $q_{(N+1)A}\isin S$ y $q_{1B}\not\isin S$ entonces $\=S=\=S'+$(ii)
1. Si $q_{(N+1)A}\not\isin S$ y $q_{1B}\isin S$ entonces $\=S=\=S'+$(iii)
1. Si $q_{(N+1)A}\isin S$ y $q_{1B}\isin S$ entonces $\=S=\=S'+$(iv)

Luego por inducción, $\forall S=\{q_{i_1C_1}, q_{i_2C_2},...\}\exist\=S:\=S\rightarrow S$

### Complejidad temporal

Por cada posible estado se realiza $O(1)$ operaciones. Y en total hay $npk^2$ estados. Complejidad temporal: $O(npk^2)$

## Podas

### Lema 1: Empezar por ejercicio con solución

Si existe una solución óptima $S$ con $c(S)\neq 0$, entonces existe una solución óptima S' tal que
$\forall i:q_{iA}\isin S'$ => $a_i=1$ $\wedge$ $q_{iB}\isin S'$ => $b_i=1$.

**Demostración:**

Sea $S$ una solución óptima tal que $c(S)\neq0$ que no cumpla $\forall i:q_{iA}\isin S$ => $a_i=1$ $\wedge$ $q_{iB}\isin S$ => $b_i=1$. Asumamos sin pérdida de generalidad que:
$q_{iA}\isin S$ $\wedge$ $a_i=0$ .

Si $q_{iA}=\{\}$, entonces $|\bigcup S|=|\bigcup S/q_{iA}|$

Como $c(S)\neq0$, $\exist j:a_j=1$ $\vee$ $b_j=1$. Sin pérdida de generalidad asumamos que $b_j=1$. Luego,
$$|\bigcup S/q_{iA}|\leq|\bigcup S/q_{iA}\cup q_{jB}|=|\bigcup S'|$$

Luego, $c(S)\leq c(S')$, pero como $S$ es óptimo: $c(S)= c(S')$

Si $q_{iA}\neq\{\}$, entonces $\exist x_1, x_2...x_r$ con $r<k,$ $x_r<k$, $\forall j,h:j<h$ => $x_j<x_h$ tal que:

$q_{iA}=\{i+x_1,i+x_2,...,i+x_r\}$

Sean $j=i+x_1$ y $y_h=x_{h+1}-x_1$. Sustituyendo

$q_{iA}= \{j,j+y_1,...,j+y_{r-1}\}$

=> $q_{iA}\subseteq q_{jA}$

=> $c(S)\leq c(S')$

Con $S'=S/q_{iA}$  $\cup$ $\{q_{jA}\}$

Pero como $S$ es óptimo: $c(S)= c(S')$

Usando este razonamiento y aplicando inducción queda demostrado.

### Lema 2: No repetir posiciones

Si una solución óptima $S$ contiene las observaciones $q_{iA}$ y $q_{iB}$, existe otra solución óptima que o no contiene a $q_{iA}$ o no contiene a $q_{iB}$.

**Demostración:**

Usando el lema 1, partamos de una solución donde todas las observaciones comiencen con una pregunta respondida.

Llamemos:
$
X_i =
\begin{cases}
\{i\} & \text{si } b_i=1\\
\{\} & \text{si } b_i=0\\
\end{cases}
$

Para cierto $i$, sean $b_i=a_i=1$. Luego:
$$c(S)=|\bigcup S|=|(\bigcup (S/q_{iB}))\cup q_{iB}|$$

$$=|(\bigcup (S/q_{iB}))\cup (\bigcup_{j=i}^{i+k-1} X_j)|$$

$$=|(\bigcup (S/q_{iB}))\cup X_i \cup (\bigcup_{j=i+1}^{i+k-1} X_j)|$$

$$\leq |(\bigcup (S/q_{iB}))\cup X_i \cup (\bigcup_{j=i+1}^{i+k} X_j)|$$

$$= |(\bigcup (S/q_{iB}))\cup X_i \cup q_{(i+1)B}|$$

$$= |(\bigcup (S'))\cup X_i|$$

Donde $S'=(S/q_{iB}) \cup q_{(i+1)B}$

Como $a_i=1$ y $q_{iA} \subseteq S'$ entonces $i$ está siendo considerada en $S'$, por lo que:

$$|(\bigcup (S'))\cup X_i|=|(\bigcup (S'))|$$

Luego:$$c(S)\leq c(S')$$

Pero $S$ es óptimo, por lo que: $$c(S)= c(S')$$

Y obtenemos una solución óptima diferente.

### Lema 3: Evitar solapamientos

Sea $S$ una solución tal que $\exist i< n-k,j\leq k:q_{iA} \isin S$ $\wedge$ $q_{(i+j)A}\isin S$. Luego, existe la solución $S'=S-\{q_{(i+j)A}\} + \{q_{(i+k)A}\}$ tal que $c(S')\geq c(S)$

**Demostración:**

$c(S)=|\bigcup (S)|=|\bigcup (S-\{q_{(i+j)A}\})\cup q_{(i+j)A}|$

Como $q_{iA} \isin S$ sea $Q=\{l:a_l=1$ $\wedge$ $i+j<l< k\}$. Luego obviamente $Q=q_{(i+j)A}/q_{iA}$:

$c(S)=|\bigcup (S-\{q_{(i+j)A}\})\cup Q|$

Pero $Q\subseteq q_{(i+kA)}$, por tanto

$c(S)\leq|\bigcup (S-\{q_{(i+j)A}\})\cup q_{(i+kA)}|$

$=>c(S)\leq S'$

Con $S'=S-\{q_{(i+j)A}\} \cup \{q_{(i+k)A}\}$

Nota: El lema se aplica homológamente a $B$

### Lema 4: Detección de soluciones inmediatas

Sea $P=(A,B,p,k)$ una instancia del problema ($A=\{j:a_j=1\}$ y $B=\{j:b_j=1\}$). Dado una caso de la forma $(i,k_A,k_B,p_i)$ del problema. Si se cumple:
$$
    [(n-i-k_A)/k]+[(n-i-k_B)/k]\leq p
$$

Entonces:

$$c(i,k_A,k_B,p)=\sum_{j=i}^n (a_i+b_i-a_ib_i)$$

**Demostración:**

Sea $p_A=[(n-i-k_A)/k]$ y $[p_B=(n-i-k_B)/k]$

La selección $S'=\{q_{(i+k_A+kj)A}:0<j<p_A\} \cup \{q_{(i+k_B+kj)B}:0<j<p_B\}$ es una seleccion válida, ya que $p_A+p_B<p$. Luego $\forall j>k_A(k_B) \exist h:j \isin q_{hA}(a_{hB}) \wedge q_{hA}(q_{hB})\isin S'$. Por lo que todos los valores a partir de la posición i pertenecen a una observación.

### Complejidad de las podas

Para el lema 1: Este lema evita calcular (ii) cuando $a_i=0$, (iii) cuando $b_i=0$ y $iv$ cuando $a_i=0$ o $b_i=0$. Al solo analizar aquellos casos donde hay una respuesta, sea $m=\sum (a_i + b_i - a_ib_i)$ la cantidad de ejercicios resueltos, la complejidad baja a  $O(mpk^2)$.

Para el lema 2: Con este lema, descartamos aquellas soluciones que usen (iv) por completo. La repercusión directa es que no se analizarán los casos donde $k_A=k_B$, a excepción del $k_A=k_B=0$. Por lo que la complejidad baja a $O(np(k^2-k+1))$.

Para el lema 3: Esto nos permite calcular ventanas solapadas, haciendo que no se aplique (ii) y (iv) si $k_A>0$, ni tampoco (iii) ni (iv) si $k_B>0$

Para el lema 4: Esta poda permite resolver problemas de forma sencilla cuando $k*p\rightarrow n$ y solo añade un preprocesamiento de tamaño $O(n)$.

![Comparacion variando el valor de k](k%20plot.png)

![Comparacion variando el valor de p](p%20plot.png)
