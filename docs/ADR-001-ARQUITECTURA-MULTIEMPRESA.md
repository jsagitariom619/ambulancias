# ADR-001 — Arquitectura clínica multiempresa sincronizada

Estado: **APROBADA POR EL PROPIETARIO; DISEÑO EN PREPARACIÓN, NO IMPLEMENTADA**.
Fecha: 2026-10-23.
Repositorio: `jsagitariom619/ambulancias`.
Rama autorizada: `codex/ambulancia-integral`.
Supabase exclusivo: `smnnsxjvgjoabgzkprwf` (`medsolution2026-ambulancia`).
Producción `main`: NO MODIFICAR.

## Decisión
Sustituir gradualmente el modelo clínico exclusivamente local por un modelo multiempresa con sincronización bidireccional, almacenamiento local cifrado para operación offline y repositorio central clínico con aislamiento estricto entre empresas. Supabase deja de estar limitado a licencias y usuarios: **se autoriza almacenar información clínica** bajo controles reforzados. La autorización no equivale a aprobación para cargar datos reales o publicar.

## Invariantes de seguridad
1. Toda fila clínica debe tener `company_id` obligatorio y referencias compuestas que impidan asociaciones entre empresas. Nunca aceptar `company_id` desde un formulario sin validar pertenencia en servidor.
2. Identidad mediante Supabase Auth; membresías activas verificadas en base de datos; permisos por empresa y función. RLS activado y políticas explícitas en todas las tablas expuestas; denegar por defecto. No confiar en `user_metadata`, almacenamiento local o filtros del frontend como controles de autorización.
3. Probar con **dos empresas, usuarios diferentes y dispositivos distintos**: SELECT, INSERT, UPDATE, DELETE, RPC, Storage, reportes y Realtime deben impedir cruces; probar también cambio de empresa, sesiones caducadas y revocación.
4. Sin claves secretas o `service_role` en el navegador. Acciones privilegiadas solo desde backend seguro, con auditoría.
5. Protección de datos sanitarios: cifrado en tránsito y reposo; evaluar cifrado adicional de campos y claves, minimización, retención, control de accesos, bitácora inalterable, recuperación y requisitos legales aplicables en Bolivia.
6. IndexedDB actual `medsolution_ambulancia_piloto_v1` no se borra ni se migra automáticamente. Antes de migrar: respaldo cifrado verificable, inventario de registros, prueba de restauración y migración idempotente con confirmación explícita del operador.
7. El uso offline debe contemplar identidad vinculada a dispositivo, desbloqueo local seguro, expiración configurable de autorizaciones, revocación diferida, cola cifrada y manejo de conflictos. No prometer revocación inmediata sin conexión.
8. Las escrituras clínicas requieren IDs globales, `company_id`, `created_by`, `updated_by`, versiones, marcas temporales UTC, eventos auditables e idempotencia. Nunca usar 'última escritura gana' para sobrescribir silenciosamente una evaluación médica.
9. Las empresas pueden compartir sus propios registros entre dispositivos autorizados; **no existe acceso cruzado**, ni siquiera mediante búsquedas, archivos adjuntos, reportes o sincronización.

## Orden de implementación y puertas de aceptación
**Fase A — Especificación y pruebas:** inventariar tablas, campos clínicos, dependencias y estado del código; diseñar esquema de compañías, membresías, roles, licencias, dispositivos, pacientes, emergencias, signos vitales, procedimientos, medicamentos, traslados, entregas, auditoría y cola de sincronización. Definir retención, conflictos, borrados y recuperación. Preparar pruebas de aislamiento automatizadas.

**Fase B — Backend aislado:** migraciones SQL versionadas, restricciones compuestas, RLS por empresa, privilegios mínimos, autenticación, administración de activaciones y auditoría. Ejecutar asesores de seguridad y pruebas adversariales antes de permitir escrituras clínicas.

**Fase C — Cliente:** autenticación, selección autorizada de empresa, cifrado local, cola offline, reconciliación segura, indicadores de sincronización y bloqueo de acceso ante autorización inválida. Sin perder datos actuales.

**Fase D — Migración controlada:** respaldar y restaurar en entorno aislado; asociar registros locales a empresa solo con consentimiento explícito; importar sin duplicar ni sobrescribir; reconciliar conteos y firmas; ensayar reversión.

**Fase E — Validación:** dos empresas aisladas; multiusuario/multidispositivo; desconexión y reconexión; conflictos concurrentes; pérdida de dispositivo; caducidad y revocación; Android/tablet/PC; revisión legal y clínica. Solo entonces solicitar autorización para publicar `main`.

## Estado verificado al crear esta decisión
Supabase `smnnsxjvgjoabgzkprwf`: accesible y saludable; esquema `public` sin tablas al momento de inspección.
Repositorio: `app.js` usa IndexedDB versión 2 y respaldo cifrado exportable; el almacenamiento local activo aún no está cifrado y no hay integración Supabase.
Esta ADR no cambia código clínico, datos, tablas, políticas, claves, despliegues ni licencias. La implementación queda pendiente.
