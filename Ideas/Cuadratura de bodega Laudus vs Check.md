---
tags:
  - flujo
estado: idea
sistemas:
  - Laudus
  - Check
  - Google Sheets
  - Monday
horario:
script:
workflow:
variable_apply:
---

# Cuadratura de bodega Laudus vs Check

## Para qué

Saber qué productos tienen distinto stock en **Laudus** (ERP) y en **Check** (sistema de bodega), para que Erick los revise y corrija. Hoy se hace en n8n ("ERICK CHECK") con una planilla; la idea es migrarlo a Python y entender mejor *por qué* se descuadran.

## Cómo se ve el mundo

- **Laudus** solo conoce el *padnumber* (código del producto) y tres cantidades: **físico** (presente), **estimado** (entrada futura) y **reservado** (salida futura). Solo el físico se compara con Check.
- **Check** sabe dónde está cada caja: *padnumber* + **ubicación** + **unidad de almacenamiento** (pallet o caja).
  - Bodegas: principal, Erick, recepción, despacho y servicio técnico.
  - Ubicaciones: `P…` son posiciones de pallet (P61, P62…); `E…` son estantería, más chicas (E001A, E001AB…). Despacho usa un prefijo propio (`TKCM…`).
  - Un mismo producto puede estar en varias ubicaciones y unidades a la vez (sobre el pallet, dentro de una caja, a piso). No hay un criterio rígido de cuándo va "a piso" (normalmente cuando saldrá de inmediato).
- Por eso el stock de Check hay que **sumar por código** antes de compararlo con Laudus.

## Cómo conversan Laudus y Check

| Evento | Quién lo crea | Dirección | Cómo |
| ------ | ------------- | --------- | ---- |
| Orden de compra (PO) | Compras en Laudus | Laudus → Check | Automático: genera un *futuro ingreso* |
| Nota de venta | Ventas en Laudus | Laudus → Check | Automático: genera una *futura salida* |
| Guía de despacho | Bodega en Laudus | Laudus → Check | **Manual, no existe integración** |

> [!warning]
> Falta la comunicación guía de despacho → Check. Si la guía baja el stock en Laudus pero nadie cierra la salida en Check (o al revés), aparece una diferencia.

### Ingreso

1. Llega la mercadería con la etiqueta de la PO.
2. Un usuario avanzado busca la PO pendiente en Check y crea una **orden de recepción**.
3. El bodeguero la recibe en el celular, comprueba lo que llegó y lo deja en la bodega de **recepción**.
4. Hace un **traslado** a la ubicación definitiva, leyendo el código de barras de la ubicación y, si va a una caja nueva, imprimiendo su etiqueta.

### Salida

1. Ventas crea la nota de venta en Laudus y avisa por Monday.
2. En Check se crea la **orden de piqueo** desde las salidas pendientes.
3. El bodeguero va a la ubicación indicada, pistolea el producto y lo lleva a **despacho** (Check crea una caja virtual de despacho).
4. Se despacha en modo despacho y Check lo da por salido.
5. Se emite la **guía de despacho** en Laudus, que baja el stock allá.

## Disparador

Manual (hoy se ejecuta a mano en n8n). A definir: diario, antes de que Erick revise.

## Pasos

1. Bajar el stock de **Laudus**: *Herramientas → Estadísticas → Stock*.
2. Bajar el stock de **Check**: *Reportes → Stock → Bajar el Excel*.
3. Pegar ambos en las pestañas `Valores_Laudus` y `Valores_check` (hoy es manual).
4. Respaldar la cuadratura anterior, limpiar las pestañas de trabajo.
5. **Laudus**: dejar código y stock, quitar filas AHN, suma y total, normalizar el código (mayúsculas, sin espacios dobles ni caracteres raros).
6. **Check**: dejar código y stock físico, sumar los códigos repetidos, mismo filtro y normalización.
7. Unir por código en `Comparacion` y calcular la diferencia (`Ultima_cuadratura`).
8. Enviar a `Diferencia_distinta_0` solo lo que no cuadra; si no hay nada, "TODO CUADRADO".
9. Comparar contra la cuadratura del día anterior: qué apareció, qué dejó de aparecer y qué cambió.

## Lee / escribe

| Sistema       | Lee                                    | Escribe                                              |
| ------------- | -------------------------------------- | ---------------------------------------------------- |
| Laudus        | Stock físico por código (reporte)      | —                                                    |
| Check         | Stock por código, ubicación y unidad   | —                                                    |
| Google Sheets | Pestañas de valores y cuadratura previa | Comparación, última cuadratura, diferencias distintas de 0 |

## Riesgos y controles

> [!warning] Errores conocidos del flujo en n8n
> - El filtro de diferencias reales usa `!includes('0')`: **descarta diferencias como 10, 20 o 105**.
> - La diferencia sale en valor absoluto, así que no se ve de qué lado sobra.
> - Códigos numéricos largos pueden perder precisión en Excel (`4134600000000` vs `4134550000000`) y contarse como dos códigos distintos.
> - Hay pestañas (`Comparacion_anterior`) que se limpian pero no se usan.

> [!info] Fuera de alcance por ahora
> - Productos que se compran en cajas de 10 y se venden sueltos: romper la caja descuadra el stock. Se ignora por ahora.
> - Check estuvo pausado un tiempo y es posible que cambien los nombres de elementos de bodega; el flujo no debe depender de ellos.
> - Estimado y reservado de Laudus no se comparan.

## Pendientes

- [ ] Decidir si se migra a Python (`flujos/…`) con ensayo por defecto y `--apply`
- [ ] Automatizar la descarga de los dos stocks (hoy se pegan a mano)
- [ ] Corregir el filtro de diferencias y conservar el signo
- [ ] Tratar los códigos como texto para no perder precisión
- [ ] Comparar contra el día anterior dentro del flujo (hoja de nuevos / resueltos)
- [ ] Analizar cuándo un producto está "a piso" y cuándo en caja, para explicar diferencias
- [ ] Definir horario y dónde se avisa a Erick
