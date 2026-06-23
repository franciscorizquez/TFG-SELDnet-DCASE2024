# TFG-SELDnet-DCASE2024

Este repositorio contiene el material técnico asociado al Trabajo Fin de Grado:

**Sistema de detección y localización de eventos sonoros basado en aprendizaje profundo**

El proyecto realiza un análisis experimental del baseline SELDnet audio-only de DCASE 2024 para la Task 3: Sound Event Localization and Detection with Source Distance Estimation.

## Contenido

- `src_base/`: versión base del código utilizado.
- `experiments/`: scripts modificados para cada familia experimental.
- `notebooks/`: cuadernos de análisis exploratorio del dataset y características FOA.
- `figures/`: figuras generadas para la memoria.
- `docs/`: resumen del baseline y resumen de resultados.

## Experimentos principales

Se incluyen modificaciones y análisis relacionados con:

- baseline SELDnet audio-only;
- escalado y ponderación de distancia;
- Squeeze-and-Excitation blocks;
- Frequency Attention;
- class weights;
- ablaciones de canales FOA e intensity vectors.

Aclaración: En cada carpeta de experiments/, están solo los .py modificados de ese experimento con respecto al baseline. No estan ni datasets, modelos entrenados, features...

## Dataset

El dataset STARSS23/DCASE 2024 no se incluye en este repositorio debido a su tamaño y a que debe obtenerse desde las fuentes oficiales de DCASE.

## Resultados

Los resultados completos se documentan en:
docs/resumen_resultados.md