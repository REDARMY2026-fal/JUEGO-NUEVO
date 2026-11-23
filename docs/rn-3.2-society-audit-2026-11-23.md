# RN 3.2 — Auditoría integral de Sociedad
Fecha: 2026-11-23
Base: `fix/rn-3.2-stabilization-2026-11-21`
Rama de auditoría: `audit/rn-3.2-society-2026-11-23`
Respaldo previo: `backup/rn-3.2-before-society-audit-2026-11-23`
Alcance: inspección estática de indicadores, pobreza, salud, educación, empleo, abastecimiento, políticas, presupuesto, persistencia y efectos macroeconómicos.
Estado: auditoría de código en curso. No se modificó `index.html`, no se alteraron partidas y no se publicó en producción.

## 1. Base y alcance

La versión 3.2 se mantiene como base porque la rama 3.5 fue creada el 17-11-2026 y diverge de 3.2: contiene 14 commits propios, pero no incluye los 54 commits de estabilización de la rama 3.2. No es una actualización lineal ni un reemplazo seguro. Se conserva 3.2 como base estable y se evaluarán sus cambios de campaña por separado.

La rama `main` tiene título 3.0 y permanece intacta.

## 2. Modelo de indicadores

Los campos sociales guardados son:
- Educación, salud y sus subindicadores: cobertura sanitaria, capacidad hospitalaria, atención primaria y personal sanitario.
- Empleo: índice estimado de ocupación (no porcentaje de ocupados).
- Demanda laboral: índice.
- Desempleo y empleo formal: porcentajes estimados.
- Pobreza: proporción estimada de población, donde menor es mejor.
- Vivienda, abastecimiento y capacitación: índices.

La interfaz distingue el índice de empleo del desempleo y muestra la pobreza como indicador de sentido inverso. El resumen nacional convierte pobreza en bienestar mediante `100 - state.poverty`. Los campos numéricos se normalizan y limitan al cargar una partida.

## 3. Hallazgos funcionales

### S1 — Diferencia entre el costo recurrente real del código y la auditoría anterior

La fórmula vigente de `expenses()` agrega:
- Salud: capacidad hospitalaria, personal sanitario, atención primaria y cobertura.
- Sociedad: educación, capacitación y vivienda.

Con los valores iniciales de RN 3.2, el cálculo estático da:
- Salud: 19,4 M por trimestre.
- Sociedad: 6,2 M por trimestre.
- Total: 25,6 M por trimestre.

El documento anterior de estabilización menciona aproximadamente 13,4 M. Ese valor no coincide con la fórmula y los valores iniciales actuales. Antes de cambiar coeficientes, hay que fijar una única convención y comprobar el costo en una partida nueva.

### S1 — Gasto social recurrente no indexado por inflación

Los costos únicos de políticas usan `investmentCost()`, que aumenta con la inflación. En cambio, el gasto recurrente de `expenses()` usa coeficientes nominales fijos. El índice de precios puede crecer durante la partida, pero el costo real de sostener servicios sociales no se actualiza.

Esto reduce progresivamente el peso real de la salud, educación y vivienda en partidas inflacionarias. Es un problema compartido con otras partidas del presupuesto (ingresos y gastos base también son nominales), por lo que no debe corregirse solo en Sociedad sin revisar el modelo fiscal completo.

### S2 — Políticas de empleo y abastecimiento son intervenciones de una sola vez

- Inserción laboral eleva demanda de trabajo, capacitación y aprobación.
- Incentivo formal eleva formalidad, reduce marginalmente desempleo y mejora inversión.
- Abastecimiento básico eleva el índice de oferta y aprobación.

No existe un programa con duración, padrón de beneficiarios, cupo de puestos, transferencias recurrentes ni seguimiento de ejecución. Los resultados indirectos se producen mediante el cierre trimestral. Es una abstracción válida para la versión actual, pero la interfaz debe evitar sugerir que son programas operativos detallados.

### S2 — Abastecimiento no modela precios ni canasta alimentaria

`foodSupply` es un índice de capacidad/oferta. Influye en pobreza, inflación y eventos agrícolas, pero no existe un precio de alimentos o canasta básica independiente. La pobreza depende del índice general de precios y salarios reales, no de una canasta de consumo diferenciada.

### S2 — Resultados sanitarios y educativos son índices agregados

Los subindicadores afectan salud y educación gradualmente al cierre trimestral. No hay población objetivo, demanda de atención, matrícula, capacidad por habitante ni distribución provincial de los resultados. La interfaz debe nombrarlos como índices estimados, no como estadísticas observadas.

### S2 — Empleo y desempleo son dos variables correlacionadas pero no una identidad contable

El índice de empleo evoluciona por demanda laboral, industria, agricultura y obras. El desempleo se actualiza por factores parecidos, pero no se deriva matemáticamente del empleo. Esto permite resultados no equivalentes; debe conservarse la distinción visual y probarse que las tendencias no diverjan de forma absurda.

## 4. Conexión con la economía

En `closeQuarter()`:
1. Se actualizan las empresas privadas y sus empleos/utilidades.
2. Se calcula el resultado fiscal con ingresos y gastos.
3. Se aplican efectos de ideología y capacidad social.
4. Se actualizan pobreza, aprobación, estabilidad e inversión.
5. Se calcula inflación, tipo de cambio y balanza de pagos.
6. Se actualizan precios, salarios, desempleo y formalidad.
7. Se amortizan créditos y se registran eventos.

La pobreza utiliza salarios reales (`wageIndex / priceIndex`), empleo, oferta alimentaria, formalidad, desempleo y vivienda. Salud depende de cobertura, capacidad, atención primaria, personal y agua. Educación depende de capacitación, administración y pobreza. Productividad depende de educación y capacitación. Por lo tanto, Sociedad sí tiene conexiones económicas y macroeconómicas, aunque varios vínculos son coeficientes agregados.

## 5. Costos de políticas

Los costos iniciales están definidos en millones de ARS y se ajustan con inflación mediante `investmentCost()`. Las políticas sociales están protegidas por `requireExecutiveAuthority()` y no pueden ejecutarse sin asumir la presidencia. El costo se descuenta antes de ejecutar la acción y se devuelve si una acción aborta con resultado falso.

La lista de acciones sociales incluye plan educativo, becas, capacitación, inserción laboral, formalización, vivienda, equipamiento hospitalario, cobertura, personal sanitario, atención primaria, agua y abastecimiento.

## 6. Persistencia y compatibilidad

- La clave principal sigue siendo `rn_save_v1`.
- `readSave()` combina los valores por defecto con la partida guardada, normaliza campos numéricos y conserva propiedades adicionales.
- Los indicadores sociales existentes se cargan y limitan a rangos válidos.
- Al iniciar una campaña nueva, el juego copia la partida anterior a `rn_previous_save_v1`; el lobby permite exportar ese respaldo.
- No hay una versión de esquema explícita. La compatibilidad se mantiene mediante valores por defecto y normalización ad hoc.

No se debe cambiar la clave, borrar campos, reiniciar partidas ni introducir una migración destructiva. Cualquier cambio de guardado requiere fixtures de partidas antiguas, nuevas, parciales y corruptas.

## 7. Pruebas pendientes antes de corregir

1. Calcular y verificar el gasto recurrente inicial con la función exacta.
2. Ejecutar cada acción social una vez y comprobar costo, efecto inmediato e historial.
3. Comprobar que una acción no se ejecuta sin autoridad presidencial.
4. Simular cierres trimestrales con inflación baja, alta y salarios por debajo/encima de precios.
5. Verificar signos y límites de pobreza, salud, educación, empleo y abastecimiento.
6. Probar estados extremos y valores no finitos.
7. Cargar fixtures de partidas previas y verificar que se conservan todos los datos no sociales.
8. Exportar y restaurar la partida desde un navegador limpio.
9. Probar vista Sociedad en móvil, tablet y escritorio.
10. Repetir pruebas de economía y elecciones para detectar regresiones.

## 8. Estado de Supabase

El proyecto Supabase `reconstruccion-nacional` está activo, pero no tiene migraciones ni esquema de RN. La partida continúa en localStorage. No se creó ninguna tabla ni se conectó el juego durante esta auditoría.

## 9. Próximas decisiones técnicas

- No cambiar los coeficientes de gasto social de forma aislada hasta decidir cómo se indexan los gastos e ingresos nominales del presupuesto.
- Añadir un desglose visible del gasto social recurrente para que el jugador entienda el costo de sostener los servicios.
- Mantener los programas sociales actuales y sus efectos; cualquier mejora debe ampliar, no eliminar, funciones.
- Después de las pruebas, evaluar un rediseño visual de Sociedad y una pasada estética global separada del balance económico.

## 10. Estado

Auditoría estática documentada. No se modificó el código del juego ni se probaron todavía las acciones en navegador. No se declara la auditoría dinámica finalizada.
