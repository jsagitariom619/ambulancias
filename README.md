# MedSolution — Ambulancia

Plataforma de gestión y atención prehospitalaria.

## Estado

**Prototipo inicial para verificar despliegue. No apto para uso clínico.**

La pantalla inicial muestra los módulos planificados, sin formularios clínicos ni datos reales.

## Despliegue

Sitio estático: servir la raíz del repositorio con Vercel, Framework Preset: Other, sin comando de build. `index.html` es el punto de entrada.

## Arquitectura prevista

- PWA responsive para móvil, tablet y escritorio.
- Datos clínicos locales, con cifrado, controles de acceso y respaldos por implementar y auditar.
- Administración central exclusiva para cuentas de empresas y activaciones, sin suscripciones mensuales.
- No guardar datos clínicos en Supabase.

## Antes de uso clínico

Implementar y validar autenticación, persistencia local segura, migraciones, respaldo/restauración, manejo offline, permisos, pruebas de pérdida de datos y flujos clínicos.
