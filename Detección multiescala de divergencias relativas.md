# Detección multiescala de divergencias relativas en acciones argentinas mediante la transformada wavelet de Mallat

## Subtítulo para la presentación

¿Cuándo una acción se aleja del mercado y cuándo vuelve a comportarse como el resto?

## Por qué elegir este nombre

El título dice exactamente qué hacemos, sin prometer más de lo que el proyecto puede demostrar:

- **Detección**: buscamos identificar momentos particulares en una señal.
- **Multiescala**: analizamos movimientos rápidos, intermedios y lentos.
- **Divergencias relativas**: no estudiamos solamente si una acción sube o baja, sino si se mueve distinto del mercado.
- **Acciones argentinas**: especificamos el universo analizado.
- **Wavelet de Mallat**: señalamos cuál es la herramienta central de la materia.

No usaría "pair trading" en el título porque no estamos comparando necesariamente dos acciones individuales. Comparamos cada acción contra un conjunto de otras acciones, es decir, contra un índice construido por nosotros.

Tampoco usaría "arbitraje", porque el arbitraje implica, en sentido estricto, una ganancia prácticamente libre de riesgo. Nuestro método solamente estudia una regularidad estadística que puede fallar.

## 1. Idea general del proyecto

Tenemos varias acciones argentinas. Aunque cada empresa tiene movimientos propios, muchas veces todas reaccionan a factores comunes:

- situación económica argentina;
- cambios en el dólar;
- elecciones;
- tasas de interés;
- noticias sobre el mercado local;
- períodos de optimismo o crisis.

Por eso, una parte importante del movimiento de una acción no es exclusiva de esa empresa: es un movimiento compartido con el resto del mercado.

El proyecto busca responder:

> Cuando una acción se aleja mucho del comportamiento general del mercado, ¿esa separación representa un cambio permanente o una desviación transitoria que luego se corrige?

Para analizarlo:

1. construimos una representación del comportamiento general del mercado;
2. medimos cuánto se separa cada acción de ese comportamiento;
3. aplicamos Mallat para estudiar esa separación en diferentes escalas;
4. detectamos divergencias anormales;
5. evaluamos si esas divergencias vuelven a niveles habituales.

## 2. Qué es una acción

Una acción representa una participación en una empresa.

Su precio cambia según lo que las personas están dispuestas a pagar por ella. Puede variar por:

- resultados de la empresa;
- expectativas futuras;
- noticias;
- cambios económicos;
- movimientos generales del mercado.

En el proyecto no intentamos estimar cuánto "vale realmente" cada empresa. Tratamos su precio como una señal temporal:

$$P_t$$

donde $P_t$ es el precio observado en la fecha $t$.

## 3. Por qué no alcanza con mirar los precios directamente

Supongamos que GGAL cae un 4 % durante un día.

Eso, por sí solo, parece negativo. Pero pueden haber ocurrido dos situaciones diferentes:

**Situación A**

- GGAL cae 4 %.
- El resto del mercado cae 8 %.

Aunque GGAL cayó, se comportó mejor que el mercado.

**Situación B**

- GGAL cae 4 %.
- El resto del mercado sube 2 %.

Ahora GGAL sí tuvo un comportamiento particularmente malo.

Por eso no queremos estudiar únicamente el movimiento absoluto de una acción. Queremos estudiar su movimiento relativo al mercado.

## 4. Qué es el índice sintético

Un índice sintético es una serie construida por nosotros para resumir el comportamiento conjunto de varias acciones.

No es un índice oficial como el S&P Merval. Es una referencia matemática creada con nuestro conjunto de datos.

Supongamos que tenemos cuatro acciones y que durante un día tuvieron estos retornos:

| Acción | Retorno diario |
|--------|----------------|
| GGAL   | +2 %           |
| BMA    | +1 %           |
| YPF    | -1 %           |
| PAMP   | +2 %           |

El retorno promedio sería:

$$r_{\text{índice},t} = \frac{2\% + 1\% - 1\% + 2\%}{4} = 1\%$$

El índice representa el rendimiento de una cartera que invierte la misma proporción en todas las acciones.

Si empieza con un valor de 1:

$$I_0 = 1$$

y el mercado sube un 1 %:

$$I_1 = 1(1 + 0.01) = 1.01$$

Si al día siguiente sube un 2 %:

$$I_2 = 1.01(1 + 0.02) = 1.0302$$

Así obtenemos una señal que resume la evolución conjunta del panel.

## 5. Por qué necesitamos un índice diferente para cada acción

No conviene comparar GGAL contra un índice que también contiene GGAL.

Si GGAL tiene un movimiento muy grande, ese mismo movimiento modifica el índice contra el que la estamos comparando. Eso reduce artificialmente la diferencia.

Por eso usamos un método llamado **leave-one-out**, que significa "dejar uno afuera".

Para analizar GGAL:

$$I_{-\text{GGAL}} = \text{índice formado por todas las acciones excepto GGAL}$$

Para analizar BMA:

$$I_{-\text{BMA}} = \text{índice formado por todas las acciones excepto BMA}$$

Cada acción tiene, entonces, su propio benchmark.

**Ejemplo**

Para analizar GGAL utilizamos:

- BMA;
- SUPV;
- YPF;
- PAMP;
- CEPU;
- LOMA;
- TGSU2.

De esta forma, el benchmark representa el movimiento del resto del mercado y no está contaminado por la misma acción que queremos analizar.

## 6. Qué es el retorno

Los precios de diferentes acciones no son directamente comparables.

Una acción puede valer $2.000 y otra $8.000, pero eso no significa que la segunda sea cuatro veces más rentable.

Por eso trabajamos con retornos:

$$r_t = \frac{P_t}{P_{t-1}} - 1$$

Si una acción pasa de $100 a $105:

$$r_t = \frac{105}{100} - 1 = 0.05$$

El retorno fue del 5 %.

Los retornos permiten combinar las acciones para construir el índice sintético.

## 7. Qué es la divergencia relativa

Una vez construido el índice, queremos medir la separación entre la acción y el mercado.

Una forma simple sería comparar sus rendimientos acumulados. Sin embargo, esa medida queda muy condicionada por la fecha inicial.

Una formulación más sólida es trabajar con logaritmos:

$$S_t = \log(P_t) - \alpha - \beta \log(I_t)$$

Esta nueva señal $S_t$ puede llamarse:

- **spread**;
- **divergencia relativa**;
- **señal relativa**;
- **residuo respecto del mercado**.

**Qué significa cada elemento**

- $P_t$: precio de la acción.
- $I_t$: índice sintético formado por las demás acciones.
- $\alpha$: diferencia de nivel entre ambas series.
- $\beta$: sensibilidad habitual de la acción frente al mercado.
- $S_t$: movimiento particular de la acción que no queda explicado por el panel.

**Qué significa beta**

No todas las acciones reaccionan igual frente al mercado.

Por ejemplo, si el mercado sube un 1 %, una acción bancaria puede subir habitualmente un 1,4 %. Otra acción puede subir solamente un 0,7 %.

El parámetro $\beta$ ajusta esa diferencia.

Si:

$$\beta = 1.4$$

significa que históricamente la acción se mueve con mayor intensidad que el índice.

No debemos confundir un movimiento normalmente más intenso con una divergencia anormal.

## 8. Qué queremos saber sobre el spread

Queremos determinar si el spread:

1. oscila alrededor de un nivel relativamente estable; o
2. se aleja de manera permanente.

Esta diferencia es fundamental.

**Caso estacionario o con reversión**

La señal se aleja, pero tiende a regresar a su zona habitual.

Esto permite estudiar eventos de divergencia y convergencia.

**Caso no estacionario**

La señal puede seguir alejándose indefinidamente.

En ese caso, no tenemos una buena justificación para esperar que vuelva.

**Explicación sencilla de estacionariedad**

No necesitamos decir que una señal estacionaria es completamente constante.

La idea intuitiva es:

> Sus propiedades generales no cambian permanentemente y la señal suele permanecer dentro de una región relativamente estable.

Aplicaremos pruebas como ADF y KPSS al spread.

La pregunta no es solamente: "¿GGAL y el mercado están correlacionados?"

La pregunta importante es: "¿La diferencia entre GGAL y el mercado se mantiene dentro de una relación estable?"

## 9. Por qué aparece Fourier

Antes de utilizar Mallat, mostramos qué ocurre con la transformada de Fourier.

Fourier representa una señal como una combinación de oscilaciones de diferentes frecuencias.

Permite distinguir:

- variaciones lentas;
- variaciones intermedias;
- variaciones rápidas.

El inconveniente es que la transformada global resume todo el período en un único espectro.

Puede decirnos: "Esta frecuencia aparece en la señal."

Pero no nos dice claramente: "Esta frecuencia apareció principalmente durante la crisis de 2018."

Las series financieras son no estacionarias porque su comportamiento cambia con el tiempo:

- hay períodos tranquilos;
- períodos de crisis;
- cambios de tendencia;
- rupturas políticas o económicas;
- cambios de volatilidad.

Por eso necesitamos una herramienta con localización temporal.

La forma correcta de explicarlo es:

> La FFT puede calcularse, pero no es suficiente para nuestro objetivo porque necesitamos conocer simultáneamente la escala del movimiento y el momento en que ocurrió.

## 10. Qué aporta Mallat

La transformada wavelet de Mallat descompone una señal en componentes de distintas escalas.

En cada nivel genera:

- una **aproximación**, que conserva los movimientos más lentos;
- un **detalle**, que contiene movimientos más rápidos.

Podemos representar la señal como:

$$S_t = A_n + D_n + D_{n-1} + \cdots + D_1$$

donde:

- $A_n$: tendencia lenta;
- $D_1$: variaciones muy rápidas;
- $D_2$: variaciones un poco más lentas;
- niveles intermedios: movimientos de mediano plazo;
- $D_n$: movimientos de escala más grande.

**Ejemplo intuitivo**

Podemos pensar la señal como una conversación grabada:

- la aproximación sería la idea general de la conversación;
- los detalles intermedios serían cambios de tema;
- los detalles finos incluirían sonidos pequeños, interrupciones y ruido.

Mallat nos permite separar esas partes.

## 11. Por qué aplicar Mallat al spread y no solamente al precio

Aplicar Mallat directamente al precio muestra:

- tendencias de la acción;
- períodos de volatilidad;
- movimientos rápidos y lentos.

Eso es válido como análisis exploratorio.

Sin embargo, nuestro problema concreto no es estudiar únicamente el precio. Queremos estudiar la diferencia entre la acción y el mercado.

Por eso la señal central debe ser:

$$S_t = \log(P_t) - \alpha - \beta \log(I_t)$$

Y aplicamos Mallat sobre $S_t$.

Así podemos separar:

- una divergencia lenta y posiblemente estructural;
- una divergencia transitoria;
- ruido diario.

## 12. Qué vamos a analizar en cada nivel

Para cada acción analizaremos:

**Aproximación**

Muestra el comportamiento lento de la relación entre la acción y el mercado.

Puede reflejar:

- cambios estructurales;
- períodos prolongados de mejor rendimiento;
- períodos prolongados de peor rendimiento.

**Detalles gruesos**

Muestran divergencias que se desarrollan durante períodos relativamente largos.

**Detalles intermedios**

Pueden representar movimientos de varias semanas o meses.

**Detalles finos**

Muestran:

- cambios rápidos;
- shocks;
- ruido de corto plazo;
- reacciones puntuales.

También podemos calcular la energía de cada nivel:

$$E_j = \sum_k |D_j[k]|^2$$

La energía permite medir cuánto aporta cada escala al movimiento total de la señal.

Esto es mejor que normalizar cada nivel por separado, porque permite comparar la importancia relativa de las escalas.

## 13. Por qué necesitamos una implementación rolling

Si aplicamos Mallat sobre toda la historia, la reconstrucción de un punto de 2018 puede utilizar indirectamente datos posteriores a 2018.

Eso está bien para describir una señal histórica completa, pero no para simular qué habríamos sabido en ese momento.

Por eso hacemos dos análisis diferentes.

**Análisis descriptivo completo**

Aplicamos Mallat sobre toda la señal para entender:

- escalas;
- eventos históricos;
- energía;
- componentes.

**Análisis rolling**

Para cada fecha $t$:

1. tomamos solamente datos hasta $t$;
2. aplicamos Mallat;
3. reconstruimos la señal;
4. guardamos el último valor;
5. avanzamos al día siguiente.

Así evitamos utilizar información futura.

Esto es más costoso computacionalmente, pero es necesario para una evaluación temporal honesta.

## 14. Qué señal filtrada construiremos

Mallat permite reconstruir una versión suavizada del spread.

Llamamos $\bar{S}_t$ a la componente lenta o filtrada.

Después calculamos:

$$e_t = S_t - \bar{S}_t$$

donde:

- $S_t$: divergencia observada;
- $\bar{S}_t$: relación suavizada;
- $e_t$: separación de corto plazo respecto de esa relación.

**Interpretación**

Si $e_t$ es muy negativo: la acción está momentáneamente por debajo de su relación suavizada con el mercado.

Si $e_t$ es muy positivo: la acción está momentáneamente por encima.

Si está cerca de cero: la acción se encuentra cerca de su relación habitual.

## 15. Qué es el z-score

Los residuos de cada acción tienen escalas diferentes.

Una desviación de 0,05 puede ser enorme para una acción y normal para otra.

Por eso normalizamos:

$$z_t = \frac{e_t - \mu_t}{\sigma_t}$$

donde:

- $e_t$: residuo actual;
- $\mu_t$: media reciente de los residuos;
- $\sigma_t$: desvío estándar reciente.

**Interpretación**

- $z_t = -2$: la acción está aproximadamente dos desvíos por debajo de su comportamiento habitual.
- $z_t = 0$: está cerca de su nivel normal.
- $z_t = 2$: está aproximadamente dos desvíos por encima.

El z-score no tiene límites. Puede superar 3 o ser menor que -3.

## 16. Qué consideraremos un evento

Definimos un evento de divergencia cuando:

$$|z_t| > 1.5$$

o, en una versión más exigente:

$$|z_t| > 2$$

**Divergencia negativa**

$z_t < -1.5$

La acción está muy por debajo de su relación habitual con el mercado.

**Divergencia positiva**

$z_t > 1.5$

La acción está muy por encima.

No debemos decidir el umbral viendo cuál genera más dinero en toda la muestra. Debemos seleccionarlo usando un período de entrenamiento o justificarlo estadísticamente.

## 17. Qué significa que la divergencia vuelva a la normalidad

Podemos definir convergencia cuando:

$$|z_t| < 0.5$$

Por ejemplo:

1. el z-score cae hasta -2;
2. detectamos una divergencia negativa;
3. durante los días siguientes vuelve a -0,4;
4. consideramos que la divergencia se corrigió.

La principal pregunta cuantitativa será:

> De las divergencias detectadas, ¿qué porcentaje vuelve a la zona normal durante los siguientes $H$ días?

Podemos utilizar horizontes como:

- 5 ruedas;
- 10 ruedas;
- 20 ruedas.

## 18. Cómo evaluaremos si Mallat aporta valor

No alcanza con mostrar gráficos bonitos.

Debemos comparar el método contra alternativas más simples.

**Método 1: spread sin filtrar**

Calculamos el z-score directamente sobre el spread.

**Método 2: filtro Butterworth**

Aplicamos el filtro de tu notebook original.

Esto incorpora el enfoque anterior y funciona como baseline de suavizado clásico.

**Método 3: Mallat**

Filtramos la señal mediante aproximaciones y detalles wavelet.

Después comparamos:

- cantidad de señales;
- porcentaje que converge;
- tiempo hasta convergencia;
- cantidad de falsas alarmas;
- estabilidad de la señal;
- anticipación o retraso;
- sensibilidad al ruido.

La pregunta no debe ser simplemente: "¿Mallat genera más retorno?"

La pregunta central debe ser:

> "¿Mallat separa mejor las divergencias transitorias del ruido y de los cambios estructurales?"

## 19. Métricas principales

**Tasa de convergencia**

$$\text{Tasa de convergencia} = \frac{\text{eventos que vuelven a la normalidad}}{\text{eventos totales}}$$

**Tiempo medio o mediano de convergencia**

Cantidad de ruedas que tarda una señal en volver a:

$$|z| < 0.5$$

Es preferible usar la mediana porque algunos eventos pueden durar muchísimo.

**Falsas alarmas**

Eventos donde:

- se detectó una divergencia;
- pero siguió alejándose;
- o no volvió dentro del horizonte analizado.

**Máxima separación posterior**

Después de detectar el evento, medimos cuánto siguió empeorando antes de converger.

Esto indica el riesgo de interpretar demasiado pronto una desviación como transitoria.

**Cantidad de eventos**

Un método que acierta el 100 % pero detecta solamente dos casos no necesariamente es útil.

## 20. Aplicación financiera opcional

Después del análisis de señales podemos mostrar una estrategia teórica.

Pero debe presentarse como una aplicación secundaria, no como prueba principal.

**Si la acción está relativamente barata**

Cuando:

$$z_t < -1.5$$

la posición teórica sería:

- comprar la acción;
- vender el índice sintético.

**Si la acción está relativamente cara**

Cuando:

$$z_t > 1.5$$

la posición sería:

- vender la acción;
- comprar el índice sintético.

**Qué significa vender en corto**

Vender en corto significa:

1. pedir prestado un activo;
2. venderlo hoy;
3. recomprarlo posteriormente;
4. devolverlo.

Se gana si el precio baja, pero existen riesgos y costos.

En nuestro proyecto sería una simulación teórica. No estamos afirmando que todas las acciones puedan venderse en corto fácilmente en la práctica.

**Por qué usar dos patas**

Supongamos que creemos que GGAL está barata respecto del mercado.

Si solamente compramos GGAL y todo el mercado cae, podemos perder aunque la relación entre GGAL y el mercado se corrija.

En cambio, si:

- compramos GGAL;
- vendemos el índice;

intentamos aislar el movimiento relativo.

El retorno aproximado sería:

$$r_{\text{relativo},t} = r_{\text{GGAL},t} - \beta \cdot r_{\text{índice},t}$$

Esta es una estrategia relativa contra un basket, no pair trading clásico.

## 21. Cómo se fusiona con el proyecto original

El proyecto original aportaba:

- construcción de regímenes;
- filtrado Butterworth;
- variables técnicas;
- validación temporal;
- Random Forest.

La nueva línea aporta:

- índice sintético;
- análisis relativo;
- Mallat;
- descomposición multiescala;
- reversión de divergencias.

**Fusión principal**

Usamos:

1. índice sintético para eliminar parte del movimiento general del mercado;
2. spread para representar el movimiento particular;
3. Butterworth como baseline;
4. Mallat como método principal;
5. detección de divergencias con z-score;
6. evaluación temporal de convergencia.

**Extensión con Random Forest**

Solamente después de completar lo anterior podemos agregar un clasificador.

La pregunta sería:

> Dada una divergencia actual, ¿volverá a la normalidad durante los próximos veinte días?

Target:

$$Y_t = \begin{cases} 1 & \text{si converge dentro de 20 días} \\ 0 & \text{si no converge} \end{cases}$$

Features posibles:

- z-score;
- pendiente de la aproximación Mallat;
- energía de $D_1$;
- energía de $D_2$;
- energía de $D_3$;
- volatilidad;
- volumen;
- gap;
- dólar;
- proximidad electoral.

El Random Forest sería una extensión. No debería ser necesario para que el proyecto principal tenga sentido.

## 22. División temporal del análisis

No debemos diseñar el método y evaluarlo sobre exactamente los mismos datos.

Una división posible sería:

**Entrenamiento**

2011–2019.

Se utiliza para:

- estimar $\alpha$ y $\beta$;
- elegir nivel de Mallat;
- elegir ventana;
- elegir umbrales;
- analizar estacionariedad.

**Validación**

2020–2021.

Se utiliza para comparar configuraciones.

**Test**

2022–2024.

Se utiliza una sola vez para reportar los resultados finales.

La división debe hacerse por fechas, nunca mezclando aleatoriamente días de distintos años.

## 23. Pasos completos del proyecto

**Paso 1 — Descargar los datos**

Descargar precios ajustados y volumen de las acciones argentinas.

Revisar:

- fechas;
- valores faltantes;
- fechas de comienzo;
- observaciones extremas;
- moneda;
- splits y dividendos.

**Paso 2 — Explorar las series**

Mostrar:

- precios normalizados con base 100;
- retornos;
- volatilidad;
- correlación entre retornos;
- gaps;
- fechas históricas relevantes.

**Paso 3 — Mostrar la limitación de Fourier**

Aplicar FFT a una serie o spread.

Explicar:

- qué frecuencias aparecen;
- por qué el espectro es global;
- por qué no permite localizar temporalmente los eventos.

**Paso 4 — Construir índices sintéticos leave-one-out**

Para cada acción:

- excluirla;
- promediar los retornos de las otras;
- acumular el retorno del índice.

**Paso 5 — Construir spreads**

Para cada acción:

- calcular log precio;
- calcular log índice;
- estimar $\alpha$ y $\beta$;
- obtener el spread.

**Paso 6 — Analizar estabilidad**

Aplicar ADF y KPSS.

Separar:

- spreads con evidencia de estabilidad;
- spreads que parecen tener cambios estructurales.

**Paso 7 — Aplicar Mallat de forma descriptiva**

Para cada spread:

- calcular aproximaciones;
- calcular detalles;
- graficar escalas;
- calcular energía;
- localizar eventos históricos.

**Paso 8 — Elegir niveles útiles**

No elegir db4 y nivel 4 solamente porque generan un gráfico atractivo.

Justificar:

- por escala temporal;
- por energía;
- por estabilidad;
- por resultados de validación.

**Paso 9 — Construir Mallat rolling**

Para cada fecha:

- utilizar solamente datos disponibles;
- reconstruir la componente suave;
- guardar el último valor.

**Paso 10 — Construir el residuo**

$$e_t = S_t - \bar{S}_t$$

**Paso 11 — Calcular z-score**

Utilizar una ventana, por ejemplo, de 60 ruedas.

**Paso 12 — Detectar eventos**

Entrada:

$$|z| > 1.5$$

Convergencia:

$$|z| < 0.5$$

**Paso 13 — Evaluar eventos**

Medir:

- tasa de convergencia;
- tiempo de convergencia;
- falsas alarmas;
- máxima separación posterior;
- resultados por acción;
- resultados por período.

**Paso 14 — Comparar métodos**

Comparar:

- señal sin filtrar;
- Butterworth;
- Mallat.

**Paso 15 — Aplicación financiera opcional**

Simular una posición relativa long–short.

Incluir:

- costos;
- cantidad de operaciones;
- Sharpe;
- drawdown;
- exposición;
- retorno relativo.

**Paso 16 — Extensión opcional con Random Forest**

Predecir qué divergencias van a converger.

## 24. Gráficos que debería tener la entrega

**Gráfico 1** — Precios normalizados de las acciones.

**Gráfico 2** — Correlación entre retornos.

**Gráfico 3** — Acción e índice sintético correspondiente.

**Gráfico 4** — Spread entre acción e índice.

**Gráfico 5** — FFT global del spread.

**Gráfico 6** — Descomposición Mallat: aproximación, detalles, energía por nivel.

**Gráfico 7** — Spread original y reconstrucción Mallat rolling.

**Gráfico 8** — Residuo y z-score.

**Gráfico 9** — Eventos de divergencia y fechas de convergencia.

**Gráfico 10** — Comparación: sin filtro, Butterworth, Mallat.

**Gráfico 11 opcional** — Resultado de la aplicación long–short.

## 25. Qué conclusiones podemos obtener

**Conclusiones válidas**

Podemos concluir cosas como:

- ciertas acciones mantienen una relación más estable con el panel;
- algunas escalas concentran una mayor proporción de energía;
- Mallat permite localizar temporalmente cambios que la FFT global no localiza;
- algunas divergencias son transitorias y otras parecen estructurales;
- Mallat reduce o aumenta falsas alarmas respecto de Butterworth;
- determinados sectores se comportan de forma más parecida entre sí.

**Conclusiones que no deberíamos afirmar**

No deberíamos decir:

- "Mallat predice el precio."
- "Toda acción que cae termina recuperándose."
- "Encontramos arbitraje seguro."
- "La estrategia siempre gana."
- "Un R² alto demuestra capacidad predictiva."
- "Fourier no puede calcularse sobre una serie no estacionaria."
- "Correlación implica cointegración."
- "El backtest demuestra que funcionará en el futuro."

## 26. Cómo presentar la motivación en palabras simples

Podemos comenzar la exposición así:

> Las acciones argentinas suelen compartir movimientos generales provocados por el contexto económico y político. Sin embargo, en determinados momentos una empresa se separa del comportamiento del resto. Nuestro objetivo es construir una señal que represente esa separación y analizarla en diferentes escalas temporales. Para eso creamos un índice sintético con el resto de las acciones, calculamos la divergencia de cada empresa respecto de ese índice y aplicamos la transformada wavelet de Mallat. Finalmente, estudiamos si las divergencias detectadas son transitorias y cuánto tardan en volver a una zona habitual.

## 27. Resumen para organizarse

**Pregunta principal**

¿Mallat ayuda a detectar divergencias transitorias entre una acción y el resto del mercado?

**Señal analizada**

Spread entre el log precio de la acción y el log de un índice sintético leave-one-out.

**Herramienta principal**

Transformada wavelet discreta mediante el algoritmo de Mallat.

**Baselines**

- spread sin filtrar;
- filtro Butterworth.

**Evento**

Z-score absoluto superior a un umbral.

**Resultado esperado**

Medir si la divergencia converge y cuánto tarda.

**Aplicación opcional**

Estrategia relativa long–short contra el índice sintético.

**Extensión opcional**

Random Forest para predecir qué divergencias convergerán.

## 28. Reparto sugerido del trabajo

**Persona 1 — Datos y exploración**

- descarga;
- precios ajustados;
- limpieza;
- retornos;
- gráficos;
- correlaciones.

**Persona 2 — Índice y spread**

- índice leave-one-out;
- estimación de beta;
- spreads;
- pruebas de estacionariedad.

**Persona 3 — Señales**

- FFT;
- Mallat;
- reconstrucción;
- energía por niveles;
- versión rolling.

**Persona 4 — Evaluación**

- z-score;
- detección de eventos;
- convergencias;
- comparación con Butterworth;
- backtest opcional.

Todos deben comprender el flujo completo, aunque cada persona implemente una parte.

## 29. Versión mínima y versión completa

**Versión mínima obligatoria**

1. Índice sintético leave-one-out.
2. Spread.
3. Pruebas de estabilidad.
4. Mallat descriptivo.
5. Mallat rolling.
6. Z-score.
7. Evaluación de convergencia.
8. Comparación con Butterworth.

**Versión completa**

Además:

1. Estrategia relativa long–short.
2. Costos.
3. División train/validation/test.
4. Random Forest.

La versión mínima ya constituye un proyecto completo y defendible para una materia de Señales. La parte de Machine Learning debe agregarse únicamente si no impide terminar correctamente el análisis central.
