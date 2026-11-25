---
tags:
  - flujo
estado: activo
sistemas:
  - Banco Central
  - Laudus
horario: Lunes a viernes, 09:00 (Chile)
script: flujos/tc_bcch.py
workflow: .github/workflows/tc-bcch.yml
variable_apply: APLICAR_TC
---

# TC BCCh

## Para qué

Carga en Laudus las paridades (USD, EUR, GBP) publicadas por el Banco Central. Migrado desde el flujo n8n "T/C BCCh -> Laudus".

## Pasos

1. Para cada moneda consulta los últimos días en el BDE del Banco Central.
2. Compara con las paridades que ya tiene Laudus.
3. Crea solo las que faltan.

## Lee / escribe

| Sistema       | Lee                 | Escribe            |
| ------------- | ------------------- | ------------------ |
| Banco Central | Paridades del BDE   |                    |
| Laudus        | Paridades actuales  | Paridades nuevas   |

> [!warning] Corta antes de escribir a medias
> Si el Banco Central está caído, una moneda es ambigua o hay un salto mayor al 15 % contra la última paridad, termina con error sin escribir nada.

Usa el token de [[Renovar token Laudus]].
