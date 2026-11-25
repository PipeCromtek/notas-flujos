---
tags:
  - flujo
estado: activo
sistemas:
  - Gmail
  - Google Sheets
  - Monday
horario: Lunes a viernes, cada hora de 09:00 a 18:00 (Chile)
script: flujos/dte_maestro.py
workflow: .github/workflows/dte-maestro.yml
variable_apply: APLICAR_DTE
---

# DTE Maestro

## Para qué

Anota en Monday las facturas de proveedores recibidas por correo, para que no se pierda ninguna. Migrado desde el flujo n8n "DTE Maestro unificado vs Monday".

## Pasos

1. Lee de Google Sheets los proveedores activos (RUT, plazo, grupo de Monday).
2. Busca en Gmail los correos del mes con XML de esos RUT y extrae los DTE (tipos 33, 34 y 61).
3. Compara con los elementos que ya existen en el tablero de Monday, en todos los grupos.
4. Crea los que faltan y manda un solo correo con el resumen.

## Lee / escribe

| Sistema       | Lee                  | Escribe              |
| ------------- | -------------------- | -------------------- |
| Google Sheets | Proveedores activos  |                      |
| Gmail         | Correos con XML DTE  | Correo de resumen    |
| Monday        | Tablero de facturas  | Elementos nuevos     |

## Código

- `flujos/dte_maestro.py`: el flujo.
- `flujos/dte_parse.py`: lectura del XML del SII (funciones puras, con tests en `flujos/tests/test_dte.py`).
- `flujos/google_api.py`, `flujos/monday_api.py`: clientes.

> [!info] Ensayo por defecto
> Sin `--apply` lee todo pero no crea ni envía nada. En Actions escribe solo si la variable del repo `APLICAR_DTE` vale `si`.

Relacionado: [[Flujos con Claude]] (ideas para clasificar los correos que no traen XML).
