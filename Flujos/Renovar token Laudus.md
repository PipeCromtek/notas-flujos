---
tags:
  - flujo
estado: activo
sistemas:
  - Laudus
  - n8n
horario: Cada hora, al minuto 15
script: flujos/laudus_token.py
workflow: .github/workflows/renovar-token.yml
variable_apply: APLICAR_TOKEN
---

# Renovar token Laudus

## Para qué

Mantiene vigente el token de la API de Laudus que usan todos los demás flujos. Reemplaza al flujo n8n "Laudus - Crear Token (cada 4 dias)".

## Reglas

Cada login invalida el token anterior, así que se hace lo menos posible:

- Desde las 14:00 renueva si el token no alcanza a durar hasta las 14:00 del día siguiente con 3 horas de margen.
- A cualquier hora renueva si faltan 3 horas o menos para el vencimiento.
- Nunca dos logins con menos de 12 horas de diferencia (un login rechazado también cuenta).

Las corridas que no renuevan solo leen fechas.

## Lee / escribe

| Sistema | Lee                        | Escribe                                |
| ------- | -------------------------- | -------------------------------------- |
| Laudus  | Login (solo al renovar)    |                                        |
| n8n     | Data Table "Laudus Token"  | Token nuevo en la Data Table           |

> [!danger] No hacer login desde otros flujos
> Cualquier otro login rompe el token de [[DTE Maestro]], [[TC BCCh]], el MCP `laudus-local` y los flujos que siguen en n8n.
