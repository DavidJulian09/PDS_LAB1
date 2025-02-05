# LABORATORIO 1 PDS

1. LIBRERIAS

En primer lugar se usaron las siguientes librerias: 

        import wfdb
        import matplotlib.pyplot as plt
        import numpy as np
        from scipy.stats import gaussian_kde
        import random

La libreria "wfdb" permite leer los registros de datos, como lo es el estudio de las señales ECG, junto con la frecuencia de muestreo de la misma. La libreria "matplotlib.pyplot" se usó para visualizar la gráfica que contenian los datos. "numpy" fue la encargada de facilitar operaciones matemáticas como lo fue los cálculos en vectores. "scipy stats.gaussian_kde" permite estimar la densidad de probabilidad, generando distribciones de tipo gauss. Finalmente "random" se empleó para introducir distintos ruidos y simular variaciones en la señal.

2. LECTURA SEÑAL ECG

Tras esto, se prosioguió con la parte de la lectura de la señal ECG y la preparación de los datos, se dio a través del código:

        record = wfdb.rdrecord(nombrearchivo)
        signal = record.p_signal[:,0]  
        fs = record.fs  
        numerodatos = len(signal) 
        limitartiempo=int(10*fs)

        time = [i / fs for i in range(numerodatos)]  
        signal = signal[:limitartiempo]
        time = time[:limitartiempo]   

En la primera línea se utiliza la biblioteca ya descrita "wfdb" para leer el archivo de physionet, de este se extrajo su frecuencia de muestreo (fs), de lo cual solo se limitó a los primeros 10 segundundos para facilitar su análisis. Posteriormente se crea un vector de tiempo en segundos, este se calculó dividiendo el indice de cada muestra entre la frecuencia de muestreo (i/fs).

3. GRAFICACIIÓN SEÑAL ORIGINAL

        plt.figure(figsize=(12, 4))
        plt.plot(time, signal, color="orange")
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Amplitud (mv)")
        plt.title("Señal Biomédica ECG bases de datos physionet")
        plt.legend()
        plt.grid()
        plt.show()

En esta parte se usa "matplotlib" para graficar la señal en función del tiempo, en donde sus ejes indican el tiempo en segundos y la Amplituden milivoltios. Lo anterior permite comprender el comportamiento de la onda en el tiempo. El uso de plt.grid añade
una cuadrícula que facilita la interpretación de los valores.

![Image](https://github.com/user-attachments/assets/2a6ba853-766c-4681-833e-ca576e693ad2)

En el electrocardiograma mostrado se logra percibir que existen picos de voltaje altos con respecto al tiempo y en realación a los demas picos, que son a su véz complejos QRS, se percibe que una amplitud de los QRS normales está alrededor de 1mV, sin embargo, hay complejos QRS que superan la amplitud de 2mV, a este fenómeno se le conoce como una estrasístole ventricular, que corresponde a una forma monomórfica ya que todos las extrosístoles poseen amplitudes positivas.

4. HISTOGRAMA DE LA SEÑAL

        plt.figure(figsize=(8, 4))
        plt.hist(signal, bins=50, color='orange', alpha=0.7, edgecolor='black', density=True)
        plt.xlabel("Amplitud de la señal")
        plt.ylabel("Frecuencia normalizada")
        plt.title("Histograma de la señal (10s)")
        plt.grid()
        plt.show()

El histograma que realizamos permite observar la distribución de amplitudes de la señal ECG. Se divide en 50 intervalos (bins=50), y la frecuencia se normaliza (density=True) para que el área total bajo el histograma sume 1. Esto permite comparar la distribución de amplitudes con una función de probabilidad. El histograma es útil para identificar patrones estadísticos en la señal, como la concentración de valores alrededor de la media. La normalización es particularmente importante cuando se desea comparar histogramas de diferentes señales o con distribuciones teóricas.

![Image](https://github.com/user-attachments/assets/e7d8cfb7-e0d5-4a66-bb24-5d58ba1771bb)

Frente al histograma se puede ver que describe una señal ECG con una distribución de amplitudes que se asemeja a una campana de Gauss, con un pico angosto y simétrico alrededor de -1. La ligera asimetría a la izquierda y la alta densidad de probabilidad en el pico indican que la señal es estable y tiene una variabilidad relativamente baja. Esto es consistente con una señal ECG limpia y bien capturada, donde la mayor parte de la actividad eléctrica del corazón se concentra en un rango estrecho de amplitudes.

5. CÁLCuLO DE ESTADÍSTICA

        suma = 0
        for i in range(len(signal)):
            suma += signal[i]
        media = suma / len(signal)
        print(f"La media de la señal es: {media:.3f}")

        vector = 0
        for _ in signal:
            vector += 1
        print(f"La longitud del vector es: {vector}")

        desviacion = 0
        for i in range(len(signal)):
            desviacion += (signal[i] - media) ** 2
        desviacion_estandar = (desviacion / len(signal)) ** 0.5
        print(f"La desviación estándar es: {desviacion_estandar:.3f}")

        coeficiente = desviacion_estandar / media if media != 0 else float("nan")
        print(f"El coeficiente es: {coeficiente:.3f}")

Aquí se calculan estadísticas clave de la señal:

Media: Representa el valor promedio de la señal. Es un indicador de la tendencia central de los datos.

Longitud del vector: Indica el número de muestras en la señal. Esto es útil para entender la resolución temporal de la señal.

Desviación estándar: Mide la dispersión de los valores alrededor de la media. Una desviación estándar alta indica que los valores están más dispersos.

Coeficiente de variación: Relaciona la desviación estándar con la media, lo que es útil para comparar la variabilidad de señales con diferentes escalas. Estos cálculos son esenciales para caracterizar la señal y entender su comportamiento estadístico.

6. FUNCIÓN DE PROBABILIDAD GAUSSIANA

        gaus = gaussian_kde(signal)
        fx = np.linspace(min(signal), max(signal), 1000)
        val = gaus(fx)
        plt.figure(figsize=(10, 5))
        plt.plot(fx, val, color="red", label="")
        plt.xlabel("Amplitud de la señal")
        plt.ylabel("Densidad de la probabilidad")
        plt.title("Función gaussiana de probabilidad de 20s")
        plt.grid()
        plt.show()

Se utiliza "gaussian_kde" para estimar la función de densidad de probabilidad (PDF) de la señal. Esta función suaviza el histograma y proporciona una curva continua que describe cómo se distribuyen las amplitudes de la señal. La gráfica resultante es una campana de Gauss, que muestra la probabilidad de que la señal tome ciertos valores de amplitud. Esto es útil para modelar la señal y compararla con distribuciones teóricas. La PDF es una herramienta poderosa en el análisis de señales, ya que permite entender la distribución subyacente de los datos.

![Image](https://github.com/user-attachments/assets/f844b03b-efbc-48e6-a6b7-e0c0639fcc9f)

En la imagen encontramos la grafica como tal de la campana de gauss, la señal dada se puede identificar como una campana de gauss leptocurtica, ya que si se observa tanto la forma de la campana  como el pico maximo se da una curtosis positiva esto significa que el  indicador estadístico tiene una  distribución de datos con un pico más pronunciado y colas más pesadas demostrando asi  una distribución normal. Todo lo anterior coincide con el diagnótico presentado con anterioridad, en donde existen picos pronunciados a causa de las extrasístoles ventriculares. Por último resalta su alto grado de concordancia con el histograma anteriormente visto.

7.  GENERACIÓN DE RUIDO

a. Ruido Gaussiano

        ruido_gauss = [random.gauss(0, 0.1) for _ in range(len(signal))]
        gauss_signal = [signal[i] + ruido_gauss[i] for i in range(len(signal))]

El ruido gaussiano se genera con media 0 y desviación estándar 0.1, simulando interferencias aleatorias comunes en señales reales. Al sumarlo a la señal original, se obtiene una señal corrupta que mantiene la forma general de la ECG pero con distorsiones suaves. Este tipo de ruido es común en sistemas electrónicos y puede ser mitigado con técnicas de filtrado.

![Image](https://github.com/user-attachments/assets/9ccdbdd7-6f5d-4779-9301-1b30fc007e0d)

El ruido gaussiano es únicamente el que presenta una distribución de Gauss, donde las variaciones electromagnéticas normalmente son muy pequeñas del orden de los microvoltios siendo despreciable en la mayoría de sistemas, este tipo de ruido es común debido a interferencias térmicas, electrónicas o ambientales, en la gráfica podemos ver una gran diferencia con la de ruido impulsivos ya que en esta los picos son mas suaves y están distribuidas a lo largo de toda la señal, aun así, se pueden notar algunos picos pronunciados en ciertos puntos, lo que sugiere que la señal base tiene momentos destacados que sobresalen incluso con la adición del ruido.

b. Ruido de Impulso

        ruido_impulso = [random.uniform(-1, 1) if random.random() < 0.05 else 0 for _ in range(len(signal))]
        impulso_signal = [signal[i] + ruido_impulso[i] for i in range(len(signal))]

Este ruido simula interferencias abruptas, como picos aleatorios. Se genera con una probabilidad del 5% en cada muestra, lo que representa eventos esporádicos pero intensos. Este tipo de ruido es común en entornos con interferencias electromagnéticas y puede ser particularmente problemático porque introduce distorsiones significativas en la señal.

![Image](https://github.com/user-attachments/assets/4adf7521-257b-4c3e-870e-8a8fdde3ada3)

La imagen nos da una gráfica que nos indica una señal con ruido de impulso  que se ve afectada por un tipo de ruido que se caracteriza por picos de tensión o sobresaltos en el suministro de energía, en la cual existen picos  muy marcados y violentos los cuales sobresalen del patrón general de la señal lo que es muy característico del ruido impulsivo.

c. Ruido Artefacto

        ruido_artefacto = signal[:]
        for _ in range(10):
            j = random.randint(0, len(signal) - 1)
           ruido_artefacto[j] += random.uniform(-2, 2)
        artefacto_signal = ruido_artefacto

El ruido de impulso  se caracteriza por su corta duración y una presión sonora que aumenta rápidamente, en la  imagen podemos ver una gráfica que nos indica una señal con ruido de impulso que se ve afectada por un tipo de ruido que se caracteriza por picos de tensión o sobresaltos en el suministro de energía, en la cual existen picos  muy marcados y violentos los cuales sobresalen del patrón general de la señal lo que es muy característico del ruido impulsivo. Este ruido se debe a precisamente las palpitaciones del paciente, ya que esto genera un leve eco en la medición.

![Image](https://github.com/user-attachments/assets/09f1dec7-ee21-4744-9bc5-2f97fe28c65c)

Como ya se sabe un ruido artefacto es un tipo de ruido que se genera artificialmente, en la siguiente imagen se observa la gráfica del ruido artefacto generado mostrando asi  una señal aleatoria que oscila dentro de un rango controlado este al ser aleatorio, significa que no sigue un patrón específico. Esto es típico en este tipo de ruido, la presencia de un ruido artefacto puede alterar significativamente la calidad de una señal ya que este al ser un ruido no deseado puede llegar aparecer en un sistema debido a diversas razones, como interferencias externas, errores en la medición o imperfecciones en el procesamiento de la señal.

8. GRAFICACIÓN SEÑALES CON RUIDO

        plt.figure(figsize=(12, 4))
        plt.plot(time, gauss_signal, color="violet")
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Amplitud (mv)")
        plt.title("Señal con ruido de gauss")

        plt.figure(figsize=(12, 4))
        plt.plot(time, impulso_signal, color="blue")
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Amplitud (mv)")
        plt.title("Señal con ruido de impulso")

        plt.figure(figsize=(12, 4))
        plt.plot(time, artefacto_signal, color="black")
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Amplitud (mv)")
        plt.title("Señal con ruido de artefacto")

Cada tipo de ruido se grafica por separado para visualizar su impacto en la señal ECG. Esto permite comparar cómo cada tipo de ruido afecta la forma de la onda. Por ejemplo, el ruido gaussiano añade fluctuaciones suaves, mientras que el ruido de impulso introduce picos abruptos. La graficación de estas señales es crucial para entender el efecto del ruido y para diseñar técnicas de filtrado adecuadas.

9. CÁLCULO DE RELACIÓN SEÑAL RUIDO

        def potencia(signal2):
            cuadrados = 0
            for i in range(len(signal2)):
                cuadrados += signal2[i] ** 2
            return cuadrados / len(signal2)

        cal_potencia = potencia(signal)
        pot_gauss = potencia(ruido_gauss)
        pot_impulso = potencia(ruido_impulso)
        pot_artefacto = potencia([artefacto_signal[i] - signal[i] for i in range(len(signal))])

El SNR es una métrica clave para evaluar la calidad de la señal. Se calcula como la relación entre la potencia de la señal original y la potencia del ruido. Un SNR alto indica que la señal es clara respecto al ruido, mientras que un SNR bajo sugiere que el ruido domina. Este cálculo es crucial para diseñar filtros y técnicas de procesamiento que mejoren la calidad de la señal. El SNR se expresa en decibelios (dB), lo que permite una comparación directa entre diferentes tipos de ruido.

10. COMPARACIÓN DE SEÑALES

        plt.figure(figsize=(12, 8))

        plt.subplot(4, 1, 1)
        plt.plot(signal, label="Señal Original", color="black")
        plt.title("Señal Original")
        plt.legend()

        plt.subplot(4, 1, 2)
        plt.plot(gauss_signal, label="Ruido Gaussiano", color="red")
        plt.title("Señal con Ruido Gaussiano")
        plt.legend()

        plt.subplot(4, 1, 3)
        plt.plot(impulso_signal, label="Ruido Impulsivo", color="blue")
        plt.title("Señal con Ruido Impulsivo")
        plt.legend()

        plt.subplot(4, 1, 4)
        plt.plot(artefacto_signal, label="Ruido de Artefacto", color="green")
        plt.title("Señal con Ruido de Artefacto")
        plt.legend()

        plt.tight_layout()
        plt.show()

En esta sección, se superponen las gráficas de la señal original y las señales con ruido en una sola figura. Esto facilita la comparación visual y permite apreciar cómo cada tipo de ruido distorsiona la señal. La organización en subgráficas (plt.subplot) es especialmente útil para presentar múltiples señales de manera ordenada y clara. Esta comparación es fundamental para evaluar el impacto del ruido y para decidir qué técnicas de procesamiento son necesarias para mejorar la calidad de la señal.

![Image](https://github.com/user-attachments/assets/48bf4fbe-d12d-4484-a4bc-ae6559672f38)

Finalmente se comparan las señales adquiridas, en donde se puede distinguir que el ruido menos significativo en nuestra simulación fue el ruido de artefacto; en contraposición, el ruido gaussiano y el ruido de impulso tienen un mayor impacto sobre la señal original: Pese a todo lo anterior en las cuantro gráficas se puede distinguir el electrocardiograma y su diagnóstico sin ninguna dificultad.
