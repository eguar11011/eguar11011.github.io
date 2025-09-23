---
layout: post
title: "softmax - Continual Learning "
date: 2025-09-22 9:46:00
description: "Olvido catastrófico visto desde el Softmax"
tags: [Memories, LifeStory]
thumbnail: me.jpg
images:
  compare: true
  slider: true
---

# Olvido catastrófico visto desde el Softmax

Veamos el olvido catastrófico desde la función de activación **softmax**, que es la que al pasarle los *logits* de mi *backbone* nos genera una probabilidad.

Es decir:

Dada una entrada \(x\), la red produce logits  

\[
z = [z_1, z_2, \ldots, z_K].
\]

El softmax transforma esto en probabilidades:

\[
p(y = k \mid x; z) = \frac{e^{z_k}}{\sum_{j=1}^K e^{z_j}}.
\]

---

## 2. Escenario sencillo

Imagina que tenemos **4 clases** en total:

- **Primera fase**: entrenamos sólo en clases \(C_1, C_2\).  
- **Segunda fase**: entrenamos sólo en clases \(C_3, C_4\).

Esto es un típico escenario de *aprendizaje secuencial*.

---

## Fase 1: Aprendemos \(C_1\) vs \(C_2\)

Supongamos que para un ejemplo de la clase \(C_1\), la red produce logits:

\[
z = [3, 1, 0, 0].
\]

Softmax:

\[
p = \frac{1}{e^3 + e^1 + e^0 + e^0} \, [e^3, e^1, e^0, e^0].
\]

Numéricamente:

- \(e^3 \approx 20.1\), \(e^1 \approx 2.7\), \(e^0 = 1\).
- Denominador = \(20.1 + 2.7 + 1 + 1 = 24.8\).
- Probabilidades:
  - \(p(C_1) \approx 0.81\)  
  - \(p(C_2) \approx 0.11\)  
  - \(p(C_3) \approx 0.04\)  
  - \(p(C_4) \approx 0.04\)

✅ La red clasifica bien entre \(C_1\) y \(C_2\).  
❌ Las clases \(C_3, C_4\) nunca se ven, así que sus logits permanecen bajos o sin entrenar.

---

## Fase 2: Aprendemos \(C_3\) vs \(C_4\)

Ahora mostramos sólo ejemplos de esas dos clases. Supongamos un ejemplo de \(C_3\). Inicialmente la red dice:

\[
z = [3, 1, 0, 0].
\]

Pero el gradiente para este ejemplo (con \(y = 3\)) es:

\[
\frac{\partial L}{\partial z_j} = p_j - \mathbf{1}\{j = 3\}.
\]

Con los valores calculados arriba:

- Para \(j = 1\): \(\partial L / \partial z_1 = 0.81 - 0 = +0.81\)  (baja \(z_1\)).  
- Para \(j = 2\): \(\partial L / \partial z_2 = 0.11 - 0 = +0.11\)  (baja \(z_2\)).  
- Para \(j = 3\): \(\partial L / \partial z_3 = 0.04 - 1 = -0.96\)  (sube mucho \(z_3\)).  
- Para \(j = 4\): \(\partial L / \partial z_4 = 0.04 - 0 = +0.04\).  

**Interpretación**: para aprender \(C_3\), el softmax **disminuye los logits de las clases previas \(C_1, C_2\)** y aumenta \(z_3\).  

→ Al repetir esto muchas veces, la red ajusta sus parámetros de forma que \(C_1\) y \(C_2\) dejan de estar bien representadas.

---

## Conclusión

Esto es **olvido catastrófico**: al entrenar en \(C_3, C_4\), los gradientes empujan a los parámetros lejos de lo que servía para \(C_1, C_2\).
