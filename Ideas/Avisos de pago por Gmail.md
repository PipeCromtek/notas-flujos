---
tags:
  - flujo
estado: en desarrollo
sistemas:
  - Monday
  - Google Sheets
  - Laudus
  - Gmail
horario:
script: flujos/pagos_resumen.py
workflow:
variable_apply:
---

# Avisos de pago por Gmail

## Para qué

Para poder avisarme a mi mismo de qué los pagos se realizaron.

Es el correo final del flujo **Monday → Laudus | Pagos**, que se está migrando desde n8n a Python (`flujos/pagos_*.py`).

## Disparador

Corre apenas se generan los pagos, al terminar de subir todo a Laudus y recopila todo lo que se hizo por elemento de Monday.

## Pasos

1. **Monday**: leer el grupo de pendientes del tablero de pagos, con sus subelementos.
2. **Planilla**: leer las pestañas Proveedores, Bancos, Cuentas, Empleados y Códigos Cuentas.
3. **Preparar**: según la columna *Receta Flujo* de cada elemento, armar el pago o comprobante (`pagos_preparar.py`). Recetas: Simple, SIN F, INVOICE, FEDEX DIN / DHL, FEDEX INTERNACIONAL, Camaggi, FORM29, Remuneraciones, Inter Cuenta.
4. **Laudus**: cargar los pagos. Si el monto de Monday difiere poco del total de la factura, se agrega una línea *Ajuste Redondeo* (`pagos_facturas.py`).
5. **Monday**: marcar *OK Laudus?* y archivar los elementos cargados.
6. **Gmail**: enviar un solo correo de resumen (`pagos_resumen.py`).

## El correo

Asunto del tipo *"Monday -> Laudus: 3 cargados, 1 sin pagar y 1 con ajuste de redondeo"*, con tres tablas:

| Tabla | Qué muestra |
| ----- | ----------- |
| Se cargaron en Laudus | Elemento (con link a Monday), receta, tipo, fecha, banco, monto y total |
| No se pudieron pagar | Elementos que siguen en pendientes y el motivo, para corregir |
| Pagados con diferencia | Los cuadrados con *Ajuste Redondeo*, para revisarlos |

Si no hubo nada que reportar, no se envía correo.

## Lee / escribe

| Sistema       | Lee                                     | Escribe                                |
| ------------- | --------------------------------------- | -------------------------------------- |
| Monday        | Grupo de pendientes y subelementos      | *OK Laudus?* y archivo de los cargados |
| Google Sheets | Proveedores, bancos, cuentas, empleados | —                                      |
| Laudus        | Facturas de proveedores                 | Pagos y comprobantes                   |
| Gmail         | PDF de liquidación (solo Camaggi)       | Correo de resumen                      |

## Riesgos y controles

> [!warning]
> Si un pago falla, el correo debe decir cuál y por qué, sin marcar como pagado en Monday.

> [!info] Ya resuelto en el código
> - Un elemento con errores no se carga: queda en pendientes y aparece en "No se pudieron pagar" con su motivo.
> - Camaggi solo se paga si la suma de sus facturas da exacto el *Monto CLP* de Monday.
> - Remuneraciones se detiene si el concepto no está en la planilla (sin cuenta de respaldo a propósito).

## Pendientes

- [x] Armar el correo de resumen (`pagos_resumen.py`)
- [x] Preparar pagos por receta (`pagos_preparar.py`, `pagos_facturas.py`, `pagos_camaggi.py`)
- [ ] Script principal que une todo (leer Monday → cargar en Laudus → archivar → enviar correo), en ensayo por defecto y `--apply` para escribir
- [ ] Workflow de GitHub Actions con su variable `APLICAR_…`
- [ ] Tests de las recetas y del resumen
- [ ] Guardar en git los archivos `pagos_*.py` (hoy están sin commit)
- [ ] Al activarlo, mover esta nota a `Flujos/` y apagar el flujo de n8n
