# MedSolution — Ambulancia

Plataforma de gestión y atención prehospitalaria.

## Estado

**Versión de desarrollo para validación visual y funcional local. No apta para uso clínico real.**

Incluye diez módulos de navegación, gestión local de pacientes, atención prehospitalaria por cuatro etapas, unidad, inventario básico, calendario, traslados y reportes agregados. Los datos clínicos permanecen en IndexedDB del dispositivo.

## Despliegue

Sitio estático: servir la raíz del repositorio con Vercel, Framework Preset: Other, sin comando de build. `index.html` es el punto de entrada.

## Arquitectura prevista

- PWA responsive para móvil, tablet y escritorio.
- Datos clínicos locales en IndexedDB. Cifrado, controles de acceso y respaldo/restauración verificables pendientes.
- Administración central exclusiva para cuentas de empresas y activaciones, sin suscripciones mensuales.
- No guardar datos clínicos en Supabase.

## Antes de uso clínico

Antes de uso clínico, implementar y validar autenticación, cifrado local, respaldo/restauración, auditoría, activaciones centrales, controles de permisos, ciclo offline y pruebas de pérdida/recuperación de datos. Revisar también el nivel de estructuración de medicamentos, procedimientos y registro ABCDE. Ver `docs/AUDITORIA_PREVIA.md`.
