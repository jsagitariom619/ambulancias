# Auditoría previa — MedSolution Ambulancia

Estado: revisión estática inicial; NO apto para uso clínico real.
Rama: `codex/ambulancia-integral`. Producción `main` permanece sin cambios.

## Inventario verificado
- `index.html`: diseño responsive, menú lateral con pestañas Inicio, Pacientes, Emergencias, Reportes, Configuración. No hay pantallas operativas para Evaluación ABCDE, Procedimientos, Traslados, Ambulancias, Inventario o entrega hospitalaria.
- `app.js`: IndexedDB `medsolution_ambulancia_piloto_v1` con dos almacenes `patients` y `emergencies`; CRUD básico, vínculos por UUID, resumen y CSV. No existe cifrado ni autenticación.
- `sw.js`: shell offline y estrategia de red con caché; no valida consistencia de datos ni garantiza durabilidad.
- `manifest.webmanifest`: manifiesto PWA sin iconos instalables completos.

## Riesgos bloqueantes
1. **Privacidad**: información en IndexedDB sin cifrado. No introducir datos personales reales.
2. **Pérdida de datos**: no hay exportación y restauración cifrada, ni verificación de integridad, ni recuperación probada.
3. **Acceso**: no hay identidad de empresa, autenticación, bloqueo local ni licencias. Supabase todavía no está conectado.
4. **Trazabilidad**: sin auditoría de modificaciones, firma/autor, fecha de creación/actualización inmutable ni estados de atención completos.
5. **Integridad clínica**: datos parciales, no hay ABCDE, Glasgow, escalas, procedimientos, administración, tiempos ni entrega.
6. **Concurrencia**: sin protección frente a ediciones simultáneas desde varias pestañas.
7. **CSV**: exportación básica no constituye historia clínica ni respaldo recuperable.
8. **PWA**: instalación y funcionamiento offline no probados en Android, iOS, Windows y tablet.
9. **UI**: confirmar accesibilidad de menú lateral y pantallas en dispositivos reales.

## Reglas de implementación
- No tocar `main` ni publicar hasta superar validación y autorización explícita.
- No modificar ni borrar la base piloto automáticamente. Toda migración debe ser aditiva, versionada y reversible.
- Supabase contendrá exclusivamente identidades empresariales, dispositivos y licencias; nunca pacientes, emergencias, observaciones, medicamentos administrados ni archivos clínicos.
- No exponer claves `service_role` ni secretos en el frontend. RLS y verificación del dispositivo en el backend.
- Diseñar contingencia de operación sin red; documentar límites de revocación offline.
- Toda exportación de datos clínicos debe ser cifrada, verificable y recuperable.
- Probar flujos y seguridad antes de quitar el bloqueo de uso clínico real.

## Criterios mínimos de aceptación
- CRUD e historial por paciente, atención prehospitalaria, ABCDE, signos vitales, Glasgow, procedimientos, medicamentos, traslados y entrega hospitalaria.
- Inventario y unidades con historial de movimientos, controles de consistencia y trazabilidad.
- Reportes imprimibles con identificación del responsable, fechas y registro completo.
- Respaldo cifrado, restauración probada, manejo de errores, validación y migraciones seguras.
- Autenticación y activación central de empresa/dispositivo con permisos y RLS; datos clínicos locales cifrados.
- Pruebas automatizadas y manuales en móvil, tablet, PC y offline, sin regresiones.

## Pruebas realizadas
Revisión estática de los cuatro archivos existentes y confirmación de la rama de desarrollo. **No se ejecutaron pruebas de navegador, seguridad, PostgreSQL ni restauración**. Ningún criterio de producción se considera aprobado.
