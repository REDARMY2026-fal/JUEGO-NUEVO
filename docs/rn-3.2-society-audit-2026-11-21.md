# Auditoría funcional y pruebas ampliadas — RN 3.2
Fecha: 2026-11-22
Rama: `fix/rn-3.2-stabilization-2026-11-21`
Entorno: despliegues de vista previa Vercel y sesiones aisladas de Firecrawl.
Estado: pruebas ampliadas; corrección del calendario electoral incluida. No se publicó en producción ni se modificó `main`.

## 1. Corrección aplicada: elección general omitida por retraso de PASO
Se reprodujo el siguiente caso:
- Una partida alcanza Año 4 / T1 y sigue en interna/PASO.
- La PASO se termina manualmente después del año general inicialmente previsto.
- La fórmula se confirma cuando `nextElectionYear` ya quedó atrás.
- Antes de la corrección, `closeQuarter()` solo ejecutaba `conductElection()` cuando `state.year === state.nextElectionYear`; por eso la elección podía quedar omitida indefinidamente.

Corrección en `index.html`: al confirmar la fórmula, si `nextElectionYear <= year`, el juego reprograma la elección general para el próximo año y registra el motivo en el historial. No modifica la regla de PASO garantizada ni fuerza un resultado en las generales.

Commit: `9b9c1470571d78588541e2d2d5451ac51b6545e4`.

## 2. Validación de la corrección en navegador
En el despliegue actualizado:
- Partida de prueba llegó a Año 4 / T1 con fase `primary_voting`, `nextPasoYear=2`, `nextElectionYear=3`.
- Se finalizó el escrutinio PASO; la candidatura del jugador avanzó y pudo elegir vicepresidente de su partido.
- Al confirmar fórmula, el estado quedó en `general_campaign` y la elección se reprogramó a Año 5.
- Tras cuatro cierres trimestrales, Año 5 / T1 activó `general_voting` y creó el escrutinio general.
- El escrutinio de primera vuelta llegó al 100% y abrió balotaje cuando ninguna fórmula cumplió los umbrales.
- El balotaje llegó al 100%; en esta ejecución el jugador ganó 51,1% contra 48,9% y el estado final fue `government`, con `inaugurated=true`.

La ruta de derrota se verificó con una prueba unitaria aislada de `finalizeElection()`: una fórmula rival ganadora produjo `electionPhase="opposition"`, `inaugurated=false`, `president` rival y `governmentParty` rival. No se modificó el resultado para forzarlo en el navegador.

## 3. Simulación macroeconómica prolongada
En una sesión aislada se cerraron 12 trimestres consecutivos, desde Año 1 / T2 hasta Año 4 / T2, sin valores no finitos ni indicadores fuera de rango (0–100) en las variables monitorizadas.

Tendencias observadas en esa partida:
- Tesoro: $18.062,4 M → $13.935,7 M.
- Deuda: se mantuvo en $6.200 M durante la ventana.
- Inflación trimestral observada: osciló aproximadamente entre 4,94% y 5,57%.
- Estabilidad: 61,14 → 61,55.
- Pobreza: 33,67 → 32,12, con pequeñas variaciones.
- Desempleo: 8,37 → 8,22.
- Educación: 56,92 → 56,08.
- Salud: 63,02 → 63,30.

Interpretación: la simulación no produjo inestabilidad numérica, pero el tesoro descendió sostenidamente. Esta ejecución no demuestra que el modelo esté equilibrado a largo plazo ni que los indicadores representen correctamente la economía real.

## 4. Interfaz móvil
En viewport de 390 × 844 px se recorrieron las nueve secciones:
- Situación nacional
- Economía
- Finanzas y deuda
- Producción
- Provincias
- Sociedad
- Gobierno
- Elecciones
- Historial

El ancho del documento y del body permaneció en 390 px en las vistas comprobadas; no se detectó desbordamiento horizontal en esa prueba.

## 5. Gobierno y guardado
- Tras una victoria electoral se verificó `inaugurated=true` y acceso a la vista Gobierno.
- La renovación de un ministro descontó $120 M del tesoro y la acción quedó registrada en el historial.
- Se guardó y recargó la partida; se recuperaron año, trimestre, fase electoral, presidente, estado de asunción, tesoro e historial.
- Pruebas realizadas en perfiles de navegador aislados; no se tocaron guardados personales.

## 6. Módulo Sociedad
La auditoría específica de Sociedad está en `docs/rn-3.2-society-audit-2026-11-21.md`. Las políticas probadas actualizaron los indicadores y descontaron costos; los servicios sociales y sanitarios tienen gastos recurrentes.

## 7. Límites pendientes
1. La derrota electoral fue verificada con prueba unitaria de la función finalizadora, no mediante una partida completa de navegador. Conviene una sesión E2E que consiga derrota de forma orgánica.
2. La prueba macroeconómica abarcó 12 trimestres, no varios mandatos.
3. No se completó una prueba de exportación y restauración del archivo de respaldo en un navegador limpio.
4. El test móvil cubrió las nueve vistas a 390 px; faltan tablet, escritorio y pruebas de interacción exhaustivas.
5. La presentación de Figma Slides requiere reparación visual. El conector Figma devolvió un límite de llamadas del plan Starter, así que no fue posible inspeccionar ni guardar los cambios restantes en esta sesión. No se declara reparada.

## 8. Conclusión
**RN 3.2: flujo positivo electoral, guardado, vistas principales y recorrido móvil parcialmente verificados; se corrigió un defecto real de calendario electoral.** No está listo para publicarse como versión definitiva. `main` y producción permanecen intactos.
