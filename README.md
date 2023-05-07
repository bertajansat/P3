PAV - P3: estimación de pitch
=============================

Esta práctica se distribuye a través del repositorio GitHub [Práctica 3](https://github.com/albino-pav/P3).
Siga las instrucciones de la [Práctica 2](https://github.com/albino-pav/P2) para realizar un `fork` de la
misma y distribuir copias locales (*clones*) del mismo a los distintos integrantes del grupo de prácticas.

Recuerde realizar el *pull request* al repositorio original una vez completada la práctica.

Ejercicios básicos
------------------

- Complete el código de los ficheros necesarios para realizar la estimación de pitch usando el programa
  `get_pitch`.

   * Complete el cálculo de la autocorrelación e inserte a continuación el código correspondiente.
   
![image](https://user-images.githubusercontent.com/127047656/235481605-888c5118-2636-4f05-b0fb-cb7cf0343b97.png)

   * Inserte una gŕafica donde, en un *subplot*, se vea con claridad la señal temporal de un segmento de
     unos 30 ms de un fonema sonoro y su periodo de pitch; y, en otro *subplot*, se vea con claridad la
	 autocorrelación de la señal y la posición del primer máximo secundario.
	 
Para la generación de las gráficas hemos decidido usar Matlab. Gracias a esto podemos observar con más claridad el segundo pico de la autocorrelación, es decir el correspondiente al pitch. 
![image](https://user-images.githubusercontent.com/127047656/235480453-d2bbc234-a37a-4f22-89fc-994f295c6ad1.png)

Estos resultados los hemos logrado a través del siguiente código: 
![image](https://user-images.githubusercontent.com/127047656/235481023-2b5fe2f5-69e1-4522-9dd2-f3aacd411314.png)
Como se puede observar, hemos tenido que coger a partir de la mitad de las muestras del resultado de xcorr(audio) ya que esta función nos daba los coeficientes de la autocorrelación entre -N+1 y N-1 y el máximo está ubicado en N. Para observar mejor la correlación tomamos el vector de forma que el máximo principal esté en 0. 

	 NOTA: es más que probable que tenga que usar Python, Octave/MATLAB u otro programa semejante para
	 hacerlo. Se valorará la utilización de la biblioteca matplotlib de Python.

   * Determine el mejor candidato para el periodo de pitch localizando el primer máximo secundario de la
     autocorrelación. Inserte a continuación el código correspondiente.
     
 ![image](https://user-images.githubusercontent.com/127047656/235481972-ed18bddc-7a2c-492c-b7a3-34ceb4fbf563.png)


   * Implemente la regla de decisión sonoro o sordo e inserte el código correspondiente.
   
![image](https://user-images.githubusercontent.com/127047656/235486066-33bf8c88-1b58-4057-8ab8-53ceeefcb071.png)

De momento hemos puesto unos valores de: u_pot = -48.7 , u_r1 = 0.93 y u_rmax = 0.45. Estos valores aún no son los definitivos y se irán cambiando durante la realización de la práctica. 

   * Puede serle útil seguir las instrucciones contenidas en el documento adjunto `código.pdf`.

- Una vez completados los puntos anteriores, dispondrá de una primera versión del estimador de pitch. El 
  resto del trabajo consiste, básicamente, en obtener las mejores prestaciones posibles con él.

  * Utilice el programa `wavesurfer` para analizar las condiciones apropiadas para determinar si un
    segmento es sonoro o sordo. 
	
	  - Inserte una gráfica con la estimación de pitch incorporada a `wavesurfer` y, junto a ella, los 
	    principales candidatos para determinar la sonoridad de la voz: el nivel de potencia de la señal
		(r[0]), la autocorrelación normalizada de uno (r1norm = r[1] / r[0]) y el valor de la
		autocorrelación en su máximo secundario (rmaxnorm = r[lag] / r[0]).
		
Conseguimos separar los distintos valores a través del comando cut y redirijiendo cada columna a un fichero de salida distinto. 		
		
![image](https://user-images.githubusercontent.com/127047656/235783905-5471c16d-73e8-414e-84e4-19b736ad13bf.png)

En estos gráficos está representado de arriba a abajo: La señal de voz, el pitch calculado por el sistema, la potencia, r[1] / r[0] y finalmente r[lag] / r[0]. Tal y como se puede observar, en los sonidos sonoras, tanto la autocorrelación normalizada de 1 como la del pitch toman valores más altos que en el caso de los sonidos sordos. A partir de estos valores podemos variar nuestros umbrales y darles un valor más preciso. 

		Puede considerar, también, la conveniencia de usar la tasa de cruces por cero.

	    Recuerde configurar los paneles de datos para que el desplazamiento de ventana sea el adecuado, que
		en esta práctica es de 15 ms.

      - Use el estimador de pitch implementado en el programa `wavesurfer` en una señal de prueba y compare
	    su resultado con el obtenido por la mejor versión de su propio sistema.  Inserte una gráfica
		ilustrativa del resultado de ambos estimadores.
		
![image](https://user-images.githubusercontent.com/127047656/235756128-cd8a64a6-9885-47d8-bbd1-fb408d4fe152.png)

En la segunda gráfica observamos el pitch calculado por nuestro sistema y en la última el calculado por Wavesurfer con la opción "Pitch Contour". A todas aquellas tramas sordas se les asigna un pitch de 0 (ya que no tienen). Tal y como se puede ver, nuestro sistema calcula de forma muy precisa el pitch del hablante. 

![image](https://user-images.githubusercontent.com/127047656/235757664-1bf109ea-6c82-4cbb-8303-25cb4de42696.png)

Para verlo de forma más cómoda hemos cambiado alguna propiedad de la gráfica haciendo que solo se muestren los valores que sean superiores a 0 y que esté la gráfica en el formato 'dots'. Hay pequeñas discrepancias pero podemos ver un sistema muy robusto. 

     
		Aunque puede usar el propio Wavesurfer para obtener la representación, se valorará
	 	el uso de alternativas de mayor calidad (particularmente Python).
  
  * Optimice los parámetros de su sistema de estimación de pitch e inserte una tabla con las tasas de error
    y el *score* TOTAL proporcionados por `pitch_evaluate` en la evaluación de la base de datos 
	`pitch_db/train`..
	
![image](https://user-images.githubusercontent.com/127047656/236036416-5262e849-6cdb-44ad-9183-f37d73765a5f.png)

Para poder llegar a los resultados adjuntados a continuación hemos decidido implementar la decisión de trama sorda o sonora a través de 3 parámetros: Potencia, r[1] / r[0] y finalmente r[lag] / r[0]. Nos hemos decantado por la opción en que la potencia sea un requerimiento obligatorio y luego que si se cumplen una de las dos condiciones sobre la autocorrelación ya consideramos la trama sonora. 
	
![image](https://user-images.githubusercontent.com/127047656/235785814-ef50abf1-3605-4529-8a06-08c5096f0731.png)

Como podemos observar obtenemos una total score de 90.92%. Esto lo hemos conseguido variando los parámetros y dejándolos de la forma siguiente: u_pot = -46 dB, u_r1 = 0.94 y u_rmax = 0.39. Además, también hemos implementado center clipping como método de preprocesado y un filtro de mediana de 3 elementos como método de postprocesado.   

Buscando formas de mejorar la puntuación total hemos implementado la función unvoiced de una forma distinta además de añadirle la tasa de cruces por cero. Ha quedado de la siguiente forma:

![image](https://user-images.githubusercontent.com/127047656/236697196-9a1a23d6-c8b9-4e1a-afa7-da5dfeec67f7.png)

![image](https://user-images.githubusercontent.com/127047656/236697228-5876e4fb-569d-4e5f-95f4-2d9b136933d6.png)

Con estas condiciones y valores hemos conseguido mejorar el score, obteniendo los siguientes resultados:

![image](https://user-images.githubusercontent.com/127047656/236697289-565ad405-993e-4d0c-accb-89c38aef5e7b.png)

Hay una mejora raonable respecto a los últimos resultados que habíamos obtenido por lo cual nos quedamos con esta versión como la versión final. En esta siguen estando implementados los métodos de pre y post procesado tal y como en el anterior caso.

Ejercicios de ampliación
------------------------

- Usando la librería `docopt_cpp`, modifique el fichero `get_pitch.cpp` para incorporar los parámetros del
  estimador a los argumentos de la línea de comandos.
  
  Esta técnica le resultará especialmente útil para optimizar los parámetros del estimador. Recuerde que
  una parte importante de la evaluación recaerá en el resultado obtenido en la estimación de pitch en la
  base de datos.

  * Inserte un *pantallazo* en el que se vea el mensaje de ayuda del programa y un ejemplo de utilización
    con los argumentos añadidos.
   
![image](https://user-images.githubusercontent.com/127047656/236697062-4af72f3a-3960-4676-824f-76456fdbb23b.png)


Llamamos a la función en el terminal y observamos el mensaje de ayuda donde nos dice como usar nuestro sistema correctamente. 

- Implemente las técnicas que considere oportunas para optimizar las prestaciones del sistema de estimación
  de pitch.

  Entre las posibles mejoras, puede escoger una o más de las siguientes:

  * Técnicas de preprocesado: filtrado paso bajo, diezmado, *center clipping*, etc.
  * Técnicas de postprocesado: filtro de mediana, *dynamic time warping*, etc.
  * Métodos alternativos a la autocorrelación: procesado cepstral, *average magnitude difference function*
    (AMDF), etc.
  * Optimización **demostrable** de los parámetros que gobiernan el estimador, en concreto, de los que
    gobiernan la decisión sonoro/sordo.
  * Cualquier otra técnica que se le pueda ocurrir o encuentre en la literatura.

  Encontrará más información acerca de estas técnicas en las [Transparencias del Curso](https://atenea.upc.edu/pluginfile.php/2908770/mod_resource/content/3/2b_PS%20Techniques.pdf)
  y en [Spoken Language Processing](https://discovery.upc.edu/iii/encore/record/C__Rb1233593?lang=cat).
  También encontrará más información en los anexos del enunciado de esta práctica.

  Incluya, a continuación, una explicación de las técnicas incorporadas al estimador. Se valorará la
  inclusión de gráficas, tablas, código o cualquier otra cosa que ayude a comprender el trabajo realizado.

  También se valorará la realización de un estudio de los parámetros involucrados. Por ejemplo, si se opta
  por implementar el filtro de mediana, se valorará el análisis de los resultados obtenidos en función de
  la longitud del filtro.
   
Como técnica de preprocesado nos hemos decantado por hacer un *center clipping*, primero probamos a usar un umbral y reducir o sumar ese umbral a la señal si su valor absoluto era mayor. Posteriormente probamos a usar un umbral a partir del cual si la señal es menor que este, la tratamos como si la señal fuera 0. Después de hacer ambos, hemos visto que nos da mejores resultados el más básico, es decir un único umbral debajo del cual todo pasa a ser 0. Lo hemos realizado de la siguiente forma:
 
![image](https://user-images.githubusercontent.com/127047656/236621515-0bcfd30c-41ed-4905-a04d-7e9657664f73.png)

También hemos probado la ventana de Hamming en vez de la rectangular pero esta da peores resultados. La implementación ha sido la siguiente:

![image](https://user-images.githubusercontent.com/127047656/236623741-4ee300cf-6312-4b25-99c4-e4b00178bbc9.png)

Finalmente hemos usado un filtro de mediana de 3 elementos para realizar el postprocesado. Esto nos permite eliminar aquellos llamados errores dobles o de mitad. Para implementarlo cojemos una muestra, la anterior y la siguiente y las ordenamos de menor a mayor, de ahí nos quedamos con la del medio. Hacemos esto con todas las muestras de la señal. El pitch debe ser una curba con variaciones muy leves y a través de de este postprocesado corregimos los cambios bruscos que puedan aparecer. Lo hemos implementado de esta forma:

![image](https://user-images.githubusercontent.com/127047656/236642039-38d8a67b-aae9-4d3b-aa78-3ceca7d8ae05.png)



Evaluación *ciega* del estimador
-------------------------------

Antes de realizar el *pull request* debe asegurarse de que su repositorio contiene los ficheros necesarios
para compilar los programas correctamente ejecutando `make release`.

Con los ejecutables construidos de esta manera, los profesores de la asignatura procederán a evaluar el
estimador con la parte de test de la base de datos (desconocida para los alumnos). Una parte importante de
la nota de la práctica recaerá en el resultado de esta evaluación.
