# RN 3.2 — Auditoría integral de Sociedad
Fecha: 2026-11-23
Base: `fix/rn-3.2-stabilization-2026-11-21`
Rama de auditoría: `audit/rn-3.2-society-2026-11-23`
Respaldo previo: `backup/rn-3.2-before-society-audit-2026-11-23`
Alcance: inspección estática de indicadores, pobreza, salud, educación, empleo, abastecimiento, políticas, presupuesto, persistencia y efectos macroeconómicos.
Estado actualizado: correcciones de Sociedad aplicadas en `index.html` sobre la rama de auditoría; verificación dinámica de interfaz y PASO completada en campañas descartables. No se usaron ni sobrescribieron partidas guardadas del usuario. No se publicó en producción.

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

### S1 — Diferencia entre el costo recurrente real del código y la auditoría anterior (resuelta)

La fórmula vigente de `expenses()` agrega:
- Salud: capacidad hospitalaria, personal sanitario, atención primaria y cobertura.
- Sociedad: educación, capacitación y vivienda.

Con los valores iniciales de RN 3.2, el cálculo estático da:
- Salud: 19,4 M por trimestre.
- Sociedad: 6,2 M por trimestre.
- Total: 25,6 M por trimestre.

El documento anterior de estabilización menciona aproximadamente 13,4 M. Ese valor no coincide con la fórmula y los valores iniciales actuales. Se verificó el cálculo con la función exacta y en navegador: salud 19,4 M; educación 5,6 M; vivienda 0,6 M; total 25,6 M por trimestre. El valor de 13,4 M de la auditoría anterior no corresponde a la fórmula y valores iniciales de esta versión.

### S1 — Gasto social recurrente no indexado por inflación (resuelta en Sociedad)

Los costos únicos de políticas usan `investmentCost()`, que aumenta con la inflación. En cambio, el gasto recurrente de `expenses()` usa coeficientes nominales fijos. El índice de precios puede crecer durante la partida, pero el costo real de sostener servicios sociales no se actualiza.

Se corrigió el componente social: `socialUpkeepBreakdown()` multiplica salud, educación, capacitación y vivienda por `priceIndex / 100` (con piso técnico de 0,01). El desglose se muestra en Sociedad. El resto del presupuesto conserva partidas nominales y debe revisarse como modelo fiscal integral; no se afirma que todo el presupuesto esté indexado.

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

## 7. Pruebas y pendientes

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

Estado: correcciones y pruebas parciales completadas; auditoría integral todavía abierta. No se publicó ni se fusionó a `main`.


## 11. Verificación dinámica añadida — 2026-11-23

### Interfaz de Sociedad
Se abrió la preview de la rama de auditoría y se inició una campaña nueva descartable. No existía una partida local válida en ese contexto de prueba, por lo que no se tocó una partida del usuario.

Resultado observado:
- Carga y lobby correctos; inicio de campaña confirmado.
- Acceso a Sociedad correcto.
- 16 indicadores mostrados con unidades diferenciadas: índices `/100` y tasas en `%`.
- Desglose trimestral mostrado: salud 19,4 M; educación y capacitación 5,6 M; vivienda 0,6 M; total adicional 25,6 M.
- Las 12 acciones sociales aparecieron bloqueadas sin presidencia, como corresponde.
- No se detectaron errores de consola durante la navegación.
- En 390 px no se observó desbordamiento general; queda una observación menor sobre descripciones extensas de programas que requiere comprobación visual más exhaustiva.

### PASO y selección de fórmula
En otra campaña descartable se avanzaron ocho trimestres hasta el año 2, T1. Se comprobaron cuatro precandidaturas y 24 distritos. Se inició el escrutinio y se usó el control «Al final» para completar el conteo.

Resultado de esa ejecución:
- Candidato de prueba: 26,9% y ganador de la interna.
- Se pasó a selección de vicepresidente.
- Se eligió a una persona de la lista partidaria y se llegó a la etapa de fórmula presidencial.
- La prueba se detuvo antes de las elecciones generales, tal como se había pedido.

Esta prueba demuestra un recorrido exitoso con victoria del jugador en PASO. No demuestra todavía el caso completo de derrota del jugador ni la campaña general/segunda vuelta.

### Simulación fiscal observada
Durante el avance de ocho trimestres de la prueba PASO, el déficit trimestral informado por la interfaz fue aproximadamente AR$433–438 M. Es una observación de una campaña y no una validación de equilibrio macroeconómico a largo plazo.

### Supabase
El proyecto `reconstruccion-nacional` está `ACTIVE_HEALTHY`. Los asesores de seguridad, rendimiento y salud devolvieron cero avisos. No hay tablas ni migraciones de RN y no se conectó el juego. La integración queda pendiente hasta definir identidad/autenticación y propiedad de partidas; no conviene habilitar guardados cloud sin ese modelo.

### Git y publicación
- La rama de auditoría sigue separada de `main`.
- Se creó `checkpoint/rn-3.2-society-paso-tested-2026-11-23` desde la rama auditada.
- La comparación con `main` informa divergencia: la rama auditada está 60 commits adelante y 2 detrás. No se debe fusionar a ciegas; hay que revisar esos commits y resolver la base antes de cualquier PR.
- No se desplegó a producción.
