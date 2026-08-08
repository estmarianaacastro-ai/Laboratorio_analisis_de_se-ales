
# Análisis de Señales en Tiempo y Frecuencia (FFT) — MATLAB

Este repositorio contiene los scripts de procesamiento digital de señales, datos experimentales (CSV) y el informe técnico para el análisis de señales periódicas en el **dominio del tiempo** y en el **dominio de la frecuencia (FFT)**.

## Descripción del Proyecto

El objetivo principal es caracterizar el comportamiento espectral y temporal de distintas ondas periódicas (senoidales, triangulares, cuadradas y trenes de pulsos). Se realiza la captura experimental mediante osciloscopio digital (**Tektronix TDS 1012B**), el procesamiento de archivos `.csv` en **MATLAB/Octave** y la comparación directa entre las amplitudes armónicas medidas y los modelos teóricos de **Series de Fourier**.

**Autores:** Mariana Castro — Nicol Martinez 

**Institución:** Universidad Militar Nueva Granada — Facultad de Ingeniería

---

##  Parámetros de la Práctica (Grupo 4)

* **Frecuencia Fundamental ($f_0$):** 2 kHZ

* **Voltaje Pico (V_p):** 2.5 V (Vpp approx 5.0 V)


* **Período (T):** 0.5ms


### Tabla de Señales Caracterizadas

| Señal | Nivel DC | Frecuencia | Voltaje Pico (V_p) | Ciclo Útil / Duty Cycle |
| --- | --- | --- | --- | --- |
| **Senoidal 1** | 0 V | 2 kHz | 2.5 V | 50% |
| **Senoidal 2** | 1.0 V | 2 kHz | 2.5 V  |  50% |
| **Triangular 1** | 0 V | 2 kHz | 2.5 V  |  50% |
| **Triangular 2** |1.5 V | 2 kHz | 2.5 V  |  50% |
| **Cuadrada 1** |0 V | 2 kHz | 2.5 V  |  50% |
| **Cuadrada 2** | 1.25 V |2 kHz | 2.5 V  |  50% |
| **Pulso 1** | 0 V | 2 kHz | 2.5 V  |  50% |
| **Pulso 2** | 0 V | 2 kHz | 2.5 V  |  20% |
| **Pulso 3** | 0 V | 2 kHz | 2.5 V  |  30% |
| **Pulso 4** | 0 V | 2 kHz | 2.5 V  |  80% |

---

## Implementación en MATLAB 

El código realiza dos tipos principales de procesamientos automatizados sobre las capturas CSV:

### 1. Análisis en el Dominio del Tiempo

Calcula magnitudes pico a pico (V_pp), busca los flancos de subida mediante umbral dinámico (`Trigger`), estima el período promedio (T) y grafica marcadores característicos sobre la señal.

```matlab
% Detección de picos y umbral de disparo
[v_max, idx_max] = max(v_raw);
[v_min, idx_min] = min(v_raw);
v_pp = v_max - v_min;

umbral = v_min + v_pp / 2;
flancos_idx = find(v_raw(1:end-1) < umbral & v_raw(2:end) >= umbral);

% Período y Frecuencia promedio
periodo_ms = mean(diff(t_ms(flancos_idx)));
frecuencia_hz = 1 / (periodo_ms * 1e-3);

```

### 2. Análisis en el Dominio de la Frecuencia (FFT)

Detecta automáticamente los armónicos en el espectro en decibelios (dB) mediante la función `findpeaks`.

```matlab
% Detección de picos armónicos
min_prominence = 15; % Prominencia en dB
min_height = -30;    % Piso de ruido en dB

[picos_Y, locs] = findpeaks(Y, 'MinPeakProminence', min_prominence, 'MinPeakHeight', min_height);
picos_X = X(locs);

```

---

##  Conclusiones Clave

1. **Frecuencia Fundamental:** La FFT confirmó la frecuencia de 2 kHz en todas las capturas, junto con sus armónicos correspondientes en múltiplos enteros.


2. **Distribución Armónica:** Las señales triangulares y cuadradas están compuestas predominantemente por armónicos impares (3f0, 5f0, 7f0), observándose una atenuación más rápida de magnitud (1/n^2) en la onda triangular frente a la cuadrada (1/n).


3. **Efecto del Ciclo de Trabajo:** En los trenes de pulsos, la variación del *duty cycle* (20%, 30%, 50%, 80%) modificó significativamente la envolvente del espectro y la distribución de nulos.


4. **Validación Teórica:** Las amplitudes medidas (V_rms y V_p) presentaron una coincidencia cercana con las Series de Fourier teóricas.
