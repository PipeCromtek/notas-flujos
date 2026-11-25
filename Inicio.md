---
tags:
  - inicio
---

# Flujos Laudus

Bóveda de notas de los flujos de automatización: qué hace cada uno, cómo se activa y qué se quiere construir.

> [!info] Dónde está el código
> En otro repo: [PipeCromtek/laudus-flujos](https://github.com/PipeCromtek/laudus-flujos), carpeta local `Desktop\Laudus\laudus-erp`. Las rutas `script` y `workflow` de cada nota (`flujos/…`, `.github/workflows/…`) son relativas a ese repo.

> [!tip] Para crear un flujo nuevo
> Crea una nota en `Ideas/` con la plantilla [[Flujo]] (comando *Insertar plantilla*). Cuando el flujo exista en código, muévela a `Flujos/` y cambia `estado` a `activo`.

## Tablero

![[Flujos.base]]

## Flujos en producción

- [[DTE Maestro]]: facturas de proveedores desde Gmail hacia Monday.
- [[Renovar token Laudus]]: mantiene vivo el token de la API.
- [[TC BCCh]]: carga las paridades del Banco Central en Laudus.

## Ideas

- [[Flujos con Claude]]: dónde podría ayudar Claude en Gmail y Laudus.

## Reglas comunes

> [!warning] Seguridad
> - Ningún secreto en el código ni en estas notas: van en *Secrets* de GitHub o en el Administrador de credenciales de Windows.
> - Todo flujo parte en **ensayo** (lee, no escribe). Escribe solo con `--apply`, que en Actions se activa con una variable del repo (`APLICAR_…=si`).
> - Un login a Laudus invalida el token anterior: los flujos reutilizan el token, nunca hacen login propio.
