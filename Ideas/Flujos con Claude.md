---
tags:
  - flujo
  - idea
estado: idea
sistemas:
  - Gmail
  - Laudus
  - Claude
horario:
script:
workflow:
variable_apply:
---

# Flujos con Claude

Lluvia de ideas. Cada una que avance pasa a su propia nota con la plantilla [[Flujo]].

## Ideas

- [ ] **Clasificar correos sin XML**: el [[DTE Maestro]] solo entiende correos con XML. Claude podría leer los demás (cotizaciones, reclamos, avisos de pago) y etiquetarlos o resumirlos.
- [ ] **Resumen diario**: un correo cada mañana con lo que hicieron los flujos, lo que falló y lo que requiere revisión humana.
- [ ] **Conciliar pagos**: cruzar avisos de transferencia que llegan por Gmail con documentos pendientes en Laudus y proponer (no registrar) el pago.

## Cómo llamar a Claude desde GitHub Actions

> [!info]
> Desde Actions se usa la API de Anthropic con una clave guardada en *Secrets* del repo (por ejemplo `ANTHROPIC_API_KEY`). freellmapi corre en el PC, así que Actions no lo puede usar; sirve para pruebas locales.

> [!warning] Datos de la empresa
> RUT, montos y nombres de clientes no se mandan a proveedores gratuitos (freellmapi), que pueden entrenar con lo que reciben. Para eso, solo la API de Anthropic.

## Reglas para cualquier flujo con IA

- La IA **propone**, el flujo valida y una persona aprueba lo que escribe en Laudus.
- Siempre empieza en ensayo, igual que los flujos existentes.
