# Resumen de resultados experimentales

Documento de apoyo para el repositorio del TFG.  
Fuente principal: `docs/Bitacora_experimental.pdf`.

Este archivo resume las métricas principales obtenidas durante la fase experimental. La bitácora completa mantiene el detalle de configuración, observaciones y análisis por experimento. Este documento se usa únicamente como índice rápido de resultados.

## Criterio de lectura de métricas

| Métrica | Interpretación |
|---|---|
| `SELD score` | Menor es mejor |
| `F-score` | Mayor es mejor |
| `Angular Error` | Menor es mejor |
| `Distance Error` | Menor es mejor |
| `Relative Distance Error` | Menor es mejor |

---

## 1. Experimentos preliminares: baseline, distancia y función de pérdida

| ID bitácora | Experimento | Épocas | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error | Observación breve |
|---|---|---:|---:|---:|---:|---:|---:|---|
| 1 | Baseline oficial DCASE2024 | 5 | 0.78 | 5.1 | 29.7° | 0.52 | 0.27 | Valida el pipeline inicial. |
| 2 | Distance Log Scaling | 5 | 0.79 | 4.5 | 29.8° | 0.59 | 0.30 | No mejora al baseline inicial. |
| 3 | Loss ponderada distancia, `dist_weight=0.5` | 5 | 0.81 | 4.8 | 31.7° | 0.88 | 0.44 | Reducir peso de distancia empeora distancia. |
| 4 | Loss ponderada distancia, `dist_weight=2.0` | 5 | 0.85 | 3.1 | 28.0° | 0.99 | 0.44 | Aumentar peso de distancia descompensa el aprendizaje. |
| 5 | Transformación `sqrt` de distancia | 5 | 0.77 | 4.8 | 28.7° | 0.77 | 0.42 | Mejora leve SELD, pero empeora distancia. |

### Conclusión parcial

Las modificaciones directas sobre la distancia no proporcionan una mejora estable. Cambiar la escala o el peso de la distancia tiende a descompensar la relación entre detección, localización angular y estimación de distancia.

---

## 2. Experimentos arquitectónicos: SE blocks y Frequency Attention

| ID bitácora | Experimento | Épocas | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error | Observación breve |
|---|---|---:|---:|---:|---:|---:|---:|---|
| 6 | SE blocks | 5 | 0.76 | 4.1 | 29.6° | 0.75 | 0.38 | Mejora SELD frente al baseline corto, pero empeora distancia. |
| 7 | SE blocks | 10 | 0.66 | 7.4 | 31.8° | 0.57 | 0.29 | SE mejora claramente con más épocas. |
| 8 | SE blocks, `lr=5e-4` | 10 | 0.75 | 5.4 | 28.7° | 0.71 | 0.36 | Learning rate menor no mejora globalmente. |
| 9 | SE + Frequency Attention, `kernel=7` | 10 | 0.74 | 7.4 | 30.4° | 0.64 | 0.29 | Mantiene F-score, no supera a SE simple. |
| 9b | SE + Frequency Attention, `kernel=15` | 10 | 0.74 | 8.7 | 31.6° | 0.67 | 0.32 | Mejora detección, empeora algo geometría. |
| 10 | SE + Frequency Attention, `kernel=15` | 20 | 0.65 | 9.2 | 32.6° | 0.53 | 0.27 | Buena configuración exploratoria: mejora detección y distancia relativa. |

### Conclusión parcial

Los bloques SE y la atención frecuencial son modificaciones prometedoras, sobre todo para mejorar detección. Sin embargo, su comparación debe hacerse con cuidado, porque muchos experimentos arquitectónicos se entrenaron durante menos épocas que el baseline largo.

---

## 3. Experimentos con class weights

| ID bitácora | Experimento | Épocas | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error | Observación breve |
|---|---|---:|---:|---:|---:|---:|---:|---|
| 11 | Class weights fuertes + SE + Frequency Attention k15 | 20 | 0.60 | 9.5 | 33.0° | 0.78 | 0.37 | Mejora detección y SELD, pero empeora distancia. |
| 12 | Class weights suavizados + SE + Frequency Attention k15 | 20 | 0.70 | 8.2 | 34.8° | 0.65 | 0.31 | No logra mejor equilibrio que los pesos fuertes. |

### Conclusión parcial

El desbalanceo de clases afecta al aprendizaje. Los pesos fuertes pueden aumentar la detección de clases minoritarias, pero introducen un trade-off: mejora el F-score y empeora la precisión espacial/distancia.

---

## 4. Entrenamiento largo y scheduler

| ID bitácora | Experimento | Épocas | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error | Observación breve |
|---|---|---:|---:|---:|---:|---:|---:|---|
| 12b | Baseline SELDnet original, entrenamiento largo | 100 | 0.57 | 14.2 | 33.7° | 0.68 | 0.32 | Referencia fuerte del proyecto. |
| 13 | Baseline SELDnet + CosineAnnealingLR | 40 | 0.62 | 10.3 | 34.6° | 0.60 | 0.30 | Mejora distancia frente a algunos entrenamientos fijos, pero baja F-score. |
| 13b | SEBlock + CosineAnnealingLR | 40 | 0.59 | 11.2 | 30.0° | 0.80 | 0.36 | Mejora detección y angular, pero empeora distancia. |

### Conclusión parcial

El número de épocas tiene un impacto muy importante. El baseline de 100 épocas se convierte en la referencia principal del proyecto. Muchas mejoras iniciales deben interpretarse como comparaciones frente a un baseline todavía infraentrenado.

---

## 5. Ablaciones FOA preliminares con SEBlock + CosineAnnealingLR

Estos experimentos fueron útiles como análisis exploratorio, pero no son la referencia final de ablaciones porque incluyen SEBlock y scheduler. Se conservan como apoyo histórico de la bitácora.

| ID bitácora | Experimento | Épocas | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error | Observación breve |
|---|---|---:|---:|---:|---:|---:|---:|---|
| 15 | W-only | 40 | 0.71 | 2.3 | 61.3° | 0.51 | 0.26 | W conserva energía, pero no espacialidad suficiente. |
| 16 | XYZ-only | 30 | 0.72 | 2.5 | 72.6° | 0.64 | 0.29 | XYZ aislado no permite localización robusta. |
| 17 | WXYZ-only, sin intensity vectors | 30 | 0.68 | 2.5 | 64.6° | 0.52 | 0.29 | Quitar IV provoca degradación espacial severa. |
| 18 | IV-only | 30 | 0.80 | 5.6 | 31.2° | 0.81 | 0.38 | IV mantiene angular, pero pierde detección/distancia. |

### Conclusión parcial

Estos resultados ya apuntaban a que los intensity vectors contienen gran parte de la información angular, pero las conclusiones definitivas se apoyan en las ablaciones finales con baseline original.

---

## 6. Ablaciones finales de entrada con baseline original

Estas son las ablaciones más limpias para discutir en la memoria: se mantiene el baseline original, se entrena durante 40 épocas y la ablación se aplica dinámicamente en el DataGenerator sin regenerar features ni cambiar arquitectura, labels, loss o métricas.

| ID bitácora | Experimento | Canales activos | Épocas | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error | Observación breve |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| 19 | WXYZ-only | W, X, Y, Z | 40 | 0.66 | 4.7 | 55.8° | 0.58 | 0.29 | Sin IV, la localización angular empeora claramente. |
| 20 | W-only | W | 40 | 0.68 | 2.5 | 64.7° | 0.54 | 0.28 | El canal W no contiene dirección explícita suficiente. |
| 21 | IV-only | IVx, IVy, IVz | 40 | 0.75 | 8.1 | 30.6° | 0.70 | 0.35 | Conserva muy bien la localización angular. |
| 22 | XYZ-only | X, Y, Z | 40 | 0.63 | 4.8 | 52.4° | 0.64 | 0.31 | Mejor que W-only, pero muy por debajo de IV-only en angular. |

### Conclusión parcial

Las ablaciones finales muestran que los intensity vectors son la fuente espacial dominante para la localización angular. W, X, Y y Z aportan información espectral, energética y contextual, pero sin los intensity vectors la localización angular se degrada notablemente.

---

## 7. Resultados seleccionados para la memoria

Tabla compacta recomendada para el capítulo de experimentación.

| Familia | Experimento | SELD score | F-score | Angular Error | Distance Error | Relative Distance Error |
|---|---|---:|---:|---:|---:|---:|
| Baseline | Baseline 5 épocas | 0.78 | 5.1 | 29.7° | 0.52 | 0.27 |
| Distancia | Log scaling | 0.79 | 4.5 | 29.8° | 0.59 | 0.30 |
| Distancia | `dist_weight=0.5` | 0.81 | 4.8 | 31.7° | 0.88 | 0.44 |
| Distancia | `dist_weight=2.0` | 0.85 | 3.1 | 28.0° | 0.99 | 0.44 |
| Distancia | Sqrt scaling | 0.77 | 4.8 | 28.7° | 0.77 | 0.42 |
| Entrenamiento | Baseline 100 épocas | 0.57 | 14.2 | 33.7° | 0.68 | 0.32 |
| Scheduler | Baseline + Cosine 40 épocas | 0.62 | 10.3 | 34.6° | 0.60 | 0.30 |
| Arquitectura | SE blocks 10 épocas | 0.66 | 7.4 | 31.8° | 0.57 | 0.29 |
| Arquitectura | SE + FreqAttention k15 20 épocas | 0.65 | 9.2 | 32.6° | 0.53 | 0.27 |
| Arquitectura | SEBlock + Cosine 40 épocas | 0.59 | 11.2 | 30.0° | 0.80 | 0.36 |
| Class weights | Pesos fuertes | 0.60 | 9.5 | 33.0° | 0.78 | 0.37 |
| Class weights | Pesos suavizados | 0.70 | 8.2 | 34.8° | 0.65 | 0.31 |
| Ablación | W-only | 0.68 | 2.5 | 64.7° | 0.54 | 0.28 |
| Ablación | XYZ-only | 0.63 | 4.8 | 52.4° | 0.64 | 0.31 |
| Ablación | WXYZ-only | 0.66 | 4.7 | 55.8° | 0.58 | 0.29 |
| Ablación | IV-only | 0.75 | 8.1 | 30.6° | 0.70 | 0.35 |

---

## 8. Lectura global de resultados

1. **El baseline largo es la referencia fuerte.**  
   El baseline entrenado durante 100 épocas obtiene el mejor SELD score global: `0.57`.

2. **El entrenamiento importa mucho.**  
   Las mejoras iniciales frente al baseline de 5 épocas deben interpretarse con cautela, porque el baseline corto estaba infraentrenado.

3. **Las modificaciones directas de distancia no son estables.**  
   Log scaling, sqrt scaling y pesos directos de distancia no mejoran de forma robusta.

4. **SE blocks y Frequency Attention son prometedores, pero no definitivos.**  
   Mejoran especialmente detección, pero no superan claramente al baseline fuerte de 100 épocas.

5. **Los class weights confirman el impacto del desbalanceo.**  
   Los pesos fuertes mejoran F-score y algunas clases minoritarias, pero deterioran distancia y geometría.

6. **Los intensity vectors son críticos para la localización angular.**  
   IV-only mantiene un error angular de `30.6°`, muy superior a W-only, XYZ-only y WXYZ-only en términos de localización.

7. **FOA log-mel e intensity vectors son complementarios.**  
   WXYZ aporta soporte espectral/energético/contextual; IV aporta información direccional explícita.

---


