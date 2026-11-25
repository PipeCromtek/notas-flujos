# Bóveda Obsidian: notas de flujos Laudus

Esta carpeta es una bóveda de Obsidian (repo privado `PipeCromtek/notas-flujos`). Usa las skills `obsidian:*` para escribir aquí: wikilinks, callouts, propiedades y `Flujos.base`.

## El código está en otro repo

- Local: `C:\Users\cromtek xx\Desktop\Laudus\laudus-erp`
- GitHub: `PipeCromtek/laudus-flujos` (privado). Nunca mezclar los dos repos en un commit.
- Las propiedades `script` y `workflow` de cada nota son rutas relativas a ese repo.

## Convenciones

- Cada flujo es una nota con la plantilla `Plantillas/Flujo.md` y la etiqueta `flujo`.
- `estado`: `idea` → `en desarrollo` → `activo` → `pausado`. Ideas en `Ideas/`, flujos con código en `Flujos/`.
- Al cambiar un flujo en el código, actualiza su nota (horario, pasos, lee/escribe).
- Nunca escribir RUT, montos de clientes, tokens ni secretos en las notas.

## Convertir una nota en flujo

Lee la nota, implementa en `laudus-erp/flujos/` siguiendo los flujos existentes (ensayo por defecto, `--apply` para escribir, token reutilizado sin login), y vuelve a la nota para dejarla en `en desarrollo` o `activo`.
