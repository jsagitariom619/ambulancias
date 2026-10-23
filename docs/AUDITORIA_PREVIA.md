# Auditoría previa — MedSolution Ambulancia

Estado: auditoría inicial ampliada tras implementación de interfaz y flujos locales; NO apto para uso clínico real.
Rama: `codex/ambulancia-integral`. Producción `main` permanece sin cambios.

## Inventario verificado
- `index.html`: tablero responsive inspirado en la referencia aprobada, navegación lateral con diez módulos, etapas de atención, formularios locales, indicadores y estados vacíos.
- `app.js`: base IndexedDB `medsolution_ambulancia_piloto_v1`; conserva `patients` y `emergencies` y amplía de forma aditiva a versión 2 con `units`, `inventory` y `appointments`. No elimina, transforma ni siembra registros existentes.
- Atención: datos de evaluación ABCDE, signos vitales, Glasgow, procedimientos, medicamentos, traslado y entrega se guardan en el registro de emergencia. Procedimientos, medicamentos y ABCDE se capturan como descripciones de texto, no como órdenes o catálogos clínicos estructurados.
- Módulos locales: pacientes (crear/editar y consultar), emergencias/atenciones (crear por cuatro etapas y continuar), traslados (consulta desde emergencias), unidad (crear/editar), inventario (agregar/retirar), calendario (programar/cancelar) y resumen CSV agregado.
- Inicio calcula conteos desde IndexedDB y fecha local del dispositivo. Los signos vitales, procedimientos, turnos y unidad se muestran desde los registros existentes; sin datos, usa estados vacíos.
- `sw.js`: shell offline con cache versionada v3; sirve el HTML en navegación sin conexión. La persistencia offline clínica y el ciclo PWA no se han validado en dispositivos reales.
- `manifest.webmanifest` e `icon.svg`: manifiesto con icono SVG; faltan pruebas de instalación en cada plataforma.
- No hay autenticación real, Supabase, cifrado local, auditoría inmutable ni respaldo/restauración cifrada. La interfaz muestra una advertencia y no se declara apta para datos clínicos reales.

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

## Verificación automatizada de la implementación
- `node --check app.js`, `node --check sw.js` y `git diff --check`: correctos.
- Prueba automatizada con JSDOM y fake IndexedDB: semilla local preexistente conservada al abrir versión 2; comprobación de los cinco almacenes; navegación a los diez módulos; creación/edición de paciente; registro de unidad, elemento de inventario y turno; flujo Evaluación → Intervenciones → Traslado → Entrega; atención final cerrada y datos persistidos; cero errores JavaScript observados.
- La prueba utiliza datos sintéticos únicamente en una base IndexedDB aislada del navegador del usuario. No se cargaron registros de ejemplo al código ni a producción.

## Validación manual pendiente
- No se obtuvieron capturas ni pruebas visuales en navegador real. Pendiente revisar computadora, tablet, Android y iPhone; menú contraído, cajón móvil, tamaños, accesibilidad y controles táctiles.
- Pendiente validar Service Worker/PWA offline e instalación en navegadores/dispositivos reales.
- Pendiente validar que la base real existente del usuario conserva todos sus registros con copia recuperable verificada antes de usar la versión en dicho dispositivo. La prueba automatizada solo comprobó una base de prueba aislada.
- No se ejecutaron pruebas de PostgreSQL/RLS/Supabase, autenticación, cifrado, respaldo/restauración ni prueba de recuperación; estos subsistemas aún no están implementados.

Los criterios clínicos y de producción siguen bloqueados. No borrar datos del sitio ni migrar a uso clínico real con esta versión.

## Respaldo cifrado local (implementado para validación)
- Exportación completa y de solo lectura de los cinco almacenes actuales, incluidos valores de claves e identificadores relacionados; no se exporta a texto claro.
- Formato JSON versionado con PBKDF2-SHA-256 (310 000 iteraciones), sal aleatoria de 128 bits, AES-256-GCM, IV aleatorio de 96 bits y hash SHA-256 del contenido autenticado. Se limita la importación a 100 MB y se valida base, versión, almacenes, key paths y claves únicas antes de escribir.
- Restauración en una transacción IndexedDB atómica, sin reemplazos: agrega claves ausentes, omite copias idénticas y aborta todo el lote ante cualquier colisión con datos distintos. No cambia el esquema ni elimina registros.
- Evidencia automatizada, con IndexedDB aislada y datos sintéticos: exportó todos los almacenes, no detectó texto clínico en claro, restauró identificadores y relación paciente-atención, rechazó contraseña incorrecta y ciphertext alterado, y confirmó abortado atómico en colisión sin cambios parciales.
- La base activa permanece sin cifrar. El respaldo cifrado no aporta cifrado en reposo para IndexedDB, autenticación, permisos, auditoría ni recuperación de contraseña. El usuario debe guardar la contraseña de respaldo por separado.

## Comparación visual de referencia y estado de prueba
- Se revisaron los diez destinos de navegación y la composición Inicio implementados en la rama contra la captura aprobada: navegación lateral, cabecera, bienvenida, botón rojo de emergencia, accesos rápidos, indicadores, unidad, listas recientes, signos vitales y turnos/procedimientos. Se mantiene la jerarquía y paleta existentes; no se hicieron ajustes visuales sin comparación en navegador real.
- Los módulos de atención siguen el flujo de cuatro etapas. La revisión visual en computadora y teléfono y las capturas quedan pendientes de inspección de navegador real.
- `sw.js` avanzó de caché shell v3 a v4 para que instalaciones existentes soliciten los archivos actualizados.
