# RN 3.2 — Auditoría de estabilización

**Fecha:** 2026-11-21  
**Base de trabajo:** `feat/qqg-rn-election-system`  
**Rama de trabajo:** `fix/rn-3.2-stabilization-2026-11-21`  
**Respaldo previo:** `backup/rn-3.2-pre-stabilization-2026-11-21`  
**Archivo modificado:** `index.html`  
**Producción / `main`:** sin cambios publicados por esta auditoría.

## Criterios de diseño electoral inspirados en QQG

RN conserva su identidad y no copia la interfaz ni el código de QQG. Se adopta la secuencia institucional que definimos:

1. El jugador crea o selecciona un personaje y un partido.
2. La interna partidaria (PASO) determina quién encabeza la fórmula.
3. La vicepresidencia se elige entre dirigentes del mismo partido.
4. Se desarrolla la campaña general.
5. Se realiza el escrutinio nacional y, cuando corresponde, el balotaje.
6. Solo el jugador cuya candidatura presidencial gana puede asumir el Poder Ejecutivo.

Perder las PASO no elimina el partido ni impide que ese partido compita en las generales. Si gana una fórmula del partido pero el personaje del jugador no encabezó la candidatura, el juego lo identifica como gobierno del partido, no como presidencia del jugador.

## Correcciones realizadas

### Elecciones y autoridad ejecutiva

- Se separaron `party` / `ideology` del jugador de `governmentParty` / `governmentIdeology` del gobierno electo.
- Se agregaron estados explícitos para `government`, `opposition` y `party_government`.
- Perder las generales ya no coloca al jugador en una fase de gobierno con poderes ejecutivos.
- Se protegieron las acciones que comprometen fondos públicos: políticas macroeconómicas y sociales, créditos, obras provinciales y urbanas, incentivos privados, gestión del gabinete y negociación de apoyos.
- Las propuestas legislativas siguen disponibles para un jugador en campaña u oposición. La composición del Congreso se trata separadamente de la presidencia.
- El gabinete del gobierno vigente deja de mostrarse como administrable cuando el jugador no es presidente.
- Se corrigió la identidad del personaje entre ciclos: el presidente rival no puede convertirse en el candidato del partido del jugador en las PASO siguientes.
- Se conservó el candidato original del jugador en el guardado mediante `playerCandidate`.
- Se añadió migración de partidas antiguas: usa el registro de PASO y la elección anterior para recuperar la identidad del jugador cuando sea posible.
- Se mantuvo la clave de guardado `rn_save_v1`; no se creó una clave nueva ni se borró el respaldo previo.

### Sociedad, economía y simplificación

- Se eliminó la aparición duplicada de **Plan educativo** en Producción; permanece en Sociedad.
- Se eliminó la aparición duplicada de **Incentivo al empleo formal** en Economía; permanece en Sociedad.
- No se eliminaron las políticas ni sus efectos: se quitó la duplicación de la interfaz.
- Se añadieron gastos de funcionamiento trimestrales vinculados a educación, capacitación, cobertura sanitaria y vivienda. La cobertura sanitaria se incorpora al mantenimiento sanitario existente.
- Se explicó en Sociedad que ampliar servicios también eleva gastos recurrentes.
- Se agregó la descripción faltante del incentivo al empleo formal.
- Se aclaró que el bloque mayoritario del Congreso no equivale necesariamente al partido del presidente.
- Se actualizaron los textos de Gobierno y el encabezado para distinguir campaña, oposición, gobierno del partido y presidencia.

### Corrección de regla electoral solicitada por el usuario (21-11-2026)

La regla institucional queda fijada sin excepciones para la candidatura presidencial del jugador:

- **Ganar las PASO es obligatorio.** Solo la candidatura ganadora puede seleccionar vicepresidente y avanzar a la campaña general.
- Si el jugador pierde las PASO, su candidatura termina en el estado `paso_lost`. No se abre la selección de vicepresidente ni la campaña general. Puede volver al inicio y crear una nueva campaña.
- Perder las elecciones generales sí es un resultado válido: el jugador pasa a la oposición.
- La regla se valida en tres lugares: resultado del escrutinio PASO, confirmación de la fórmula y creación de las elecciones generales.
- Se retiró el bypass que permitía marcar una fórmula como candidatura del jugador por la condición de límite de mandatos. La candidatura sucesora también debe ganar las PASO.
- Las partidas guardadas que registran una derrota PASO pero quedaron en fases posteriores se migran a `paso_lost`, sin conservar una vicepresidencia inválida.

Pruebas de lógica tras la corrección:
- Victoria PASO: `vice_selection`, `pasoQualified=true`.
- Derrota PASO: `paso_lost`, `pasoQualified=false`, sin vicepresidente ni acceso a generales.
- Guardado antiguo con derrota PASO y fase general: se migra a `paso_lost`.
- Pantalla de derrota: muestra el fin de candidatura y botón para volver al inicio; no muestra selector de vicepresidente.
- JavaScript vuelve a compilar sin errores.

## Pruebas realizadas

### Código y estructura

- El JavaScript de la página se compiló correctamente con el parser de V8 después de las modificaciones.
- No se detectaron nombres de funciones duplicados.
- No se detectaron IDs estáticos duplicados en el HTML.
- No se detectaron acciones públicas duplicadas entre las llamadas a `renderActions`.
- La clave de guardado `rn_save_v1` continúa presente.
- La rama de trabajo difiere del respaldo únicamente en `index.html` en esta etapa.

### Pruebas de lógica

- Resultado con victoria del jugador: fase `government`, `inaugurated=true`.
- Victoria de otro partido: fase `opposition`, `inaugurated=false`, el partido del jugador se conserva.
- Victoria de otra candidatura del partido del jugador: fase `party_government`, `inaugurated=false`.
- Migración simulada de una partida antigua tras perder: recupera el partido y el personaje del jugador, conserva el partido e ideología del gobierno electo y bloquea la autoridad ejecutiva.
- Siguiente PASO tras perder: la precandidatura vuelve a ser el personaje del jugador, no el presidente rival.
- El guard de autoridad ejecutiva devuelve permiso únicamente cuando `inaugurated=true`.
- El costo de funcionamiento social añadido equivale a aproximadamente **$13,4 M por trimestre** con los indicadores iniciales y sin intereses ni amortizaciones. Es un costo marginal deliberadamente moderado.

### Navegador aislado

- La vista previa de Vercel respondió HTTP 200 y mostró el título de RN 3.2.
- Se creó una campaña descartable con partido personalizado y se verificó el flujo de confirmación.
- Antes de asumir, se comprobaron bloqueos de políticas, deuda, inversiones privadas, obras públicas, gabinete y negociación ejecutiva.
- Se comprobó que las propuestas legislativas siguen disponibles.
- Se avanzó hasta las PASO del año 2, trimestre 1; el escrutinio se completó y abrió la selección de vicepresidente.
- El selector de vicepresidente mostró únicamente integrantes del partido del jugador.
- Se confirmó la fórmula y se abrió la campaña general.
- Se avanzó hasta las elecciones generales del año 3, trimestre 1: se mostraron 7 fórmulas y 24 distritos.
- En una sesión nueva se verificaron la nota de costos recurrentes de Sociedad y el bloqueo de sus políticas antes de asumir.
- Se guardó una campaña de prueba, se recargó el navegador y se recuperó la misma partida con candidato, partido, año y trimestre intactos.
- Las sesiones de navegador no reportaron errores de consola durante las pruebas descritas.

## Pendiente de validar antes de integrar

- Completar el escrutinio general en navegador y recorrer el balotaje real. La sesión interactiva expiró después de abrir las generales; los resultados de victoria, derrota y gobierno del partido se comprobaron además con pruebas de lógica.
- Repetir en navegador el siguiente ciclo PASO después de una derrota general, usando una partida con resultado ya guardado.
- Probar una partida antigua real exportada por el usuario; la migración se probó con un fixture sintético y no se sobrescribió ninguna partida real.
- Completar pruebas visuales en distintos anchos de pantalla y revisar los textos de todas las pantallas.
- Revisar el modelo de mayorías parlamentarias y el gabinete NPC cuando gana un partido distinto. La presidencia y el bloque legislativo ya se distinguen en la interfaz, pero la composición legislativa sigue siendo una simulación separada.
- Hacer una pasada final de equilibrio fiscal y social tras observar varias partidas largas.

## Estado de publicación

Esta rama tiene una vista previa propia de Vercel. No se fusionó con `main` ni se publicó en producción. El respaldo previo permanece disponible.
