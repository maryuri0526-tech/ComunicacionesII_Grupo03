# Bitácora del proyecto — Escenario S1, Grupo 03

Registro de decisiones, cambios y resultados. Las entradas entre corchetes deben completarse con la información real del equipo.

---

## [fecha] — Asignación del escenario y ficha de decisión técnica

- **Escenario asignado:** S1, telemetría industrial bajo perturbaciones impulsivas.
- **Problema identificado (P1):** perturbaciones breves de gran amplitud degradan muestras aisladas de la señal.
- **Decisiones de la ficha:**
  - Modulación BPSK.
  - Archivo base `Modulaciones_basadas_en_constelaciones.grc`, con Rs = 32 ksym/s, SPS = 8 y fs = 256 kS/s.
  - Pulso rectangular.
  - Perturbación: ruido impulsivo periódico controlado.
  - Variable experimental: amplitud del impulso.
- **Hipótesis H1:** a mayor amplitud, perturbaciones temporales más fuertes y mayor dispersión de la constelación.
- **Evidencia:** `docs/ficha_decision_tecnica.pdf`.

## 2026-11-08 — Investigación y fundamento teórico

- Se revisaron fuentes sobre ruido impulsivo industrial: Middleton, Blackard et al., Chilo et al., Zimmermann y Dostert, Ghosh y Pighi et al. (ver `referencias/fuentes_y_enlaces.md`).
- Se comparó BPSK con OOK. Se mantuvo BPSK por su mayor separación entre símbolos (3 dB) y su umbral fijo en cero.
- Se verificó la coherencia de los parámetros: Rb = 32 kb/s, fs = 32 000 × 8 = 256 kS/s y ancho de banda ≈ 2Rs = 64 kHz.

## 2026-11-08 — Construcción del flujograma

- Se partió de una copia de `Modulaciones_basadas_en_constelaciones_AWGN.grc`. Los originales se conservaron sin cambios en `grc/archivos_originales_o_referencia/`.
- **Cambio crítico:** la tabla de constelación por defecto era OOK `(0, 1)` y se cambió a BPSK `(-1, 1)`.
- Se agregó el generador de impulsos: `Vector Source` con W = 4 muestras en uno y N_T = 500, multiplicado por `A_imp`.
- Se agregó el receptor: filtro acoplado (promedio de 8), `Keep 1 in N`, `Binary Slicer` y medición de BER con `XOR` y un promedio de 10⁵ bits.
- Se agregó a mano el `QT GUI Eye Sink`. Hubo que corregir `sps` por `Sps`, porque GNU Radio distingue mayúsculas.
- **Validación:** con `A_imp = 0` no hubo errores y la alineación de bits quedó en `retardo_bits = 0`.

## 2026-11-08 — Pruebas

| A_imp | BER medida |
|---|---|
| 0; 0,5; 1,0; 1,5; 1,8 | sin errores en 10⁵ bits |
| 1,9 | 2,8 × 10⁻⁴ |
| 2,0 | 5,82 × 10⁻³ |
| 2,1 | 1,07 × 10⁻² |
| 2,2 a 5,0 | 1,1 × 10⁻² |

- **Observación:** el piso medido (0,011) fue mayor que el previsto con bits equiprobables (0,008). Se explicó por la secuencia de prueba periódica de 1000 bits: los impulsos alcanzan siempre los mismos 16 símbolos, y 11 de ellos valen −1.
- **Observación:** la PSD no mostró diferencias apreciables entre pruebas, porque el ciclo útil del impulso es solo del 0,8 %.
- **Corrección:** los archivos de las constelaciones de decisión y sobremuestreada estaban intercambiados. Se renombraron según el título de cada captura.

## 2026-11-08 — Informe IEEE

- Se redactó el informe en LaTeX (Overleaf) y se ajustó a 7 páginas.
- Se reorganizaron las figuras para que cada una quede junto a su análisis.

## [fecha] — Prueba exploratoria en hardware

- Se transmitió un mensaje en 16QAM entre dos HackRF One conectadas a computadores diferentes.
- Con las placas distanciadas, la constelación no era reconocible, aunque los mensajes confirmaban la transmisión.
- Al acercar las placas, se observaron los 16 grupos de la constelación.
- **Trabajo futuro:** implementar en hardware el enlace BPSK del escenario S1. Requiere interpolar de 256 kS/s a por lo menos 2 MS/s, la tasa mínima de la HackRF.

## Uso de herramientas de IA

Se usó un asistente de IA (Claude, de Anthropic) para la búsqueda de referencias, mejora de la redacción, ayuda con la construcción del flujograma modificado y algunas figuras. El equipo ejecutó las simulaciones y validó los resultados.
