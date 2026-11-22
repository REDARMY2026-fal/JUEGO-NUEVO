# Auditoría de Sociedad — RN 3.2
Fecha: 2026-11-21
Rama: `fix/rn-3.2-stabilization-2026-11-21`
Entorno: vista previa Vercel aislada + inspección de `index.html`
Estado: auditoría funcional parcial; no se modificó la lógica social.

## 1. Indicadores presentes
La vista Sociedad expone indicadores de mercado laboral, sistema sanitario, educación, pobreza, vivienda y abastecimiento. Se verificó la navegación a Sociedad y la renderización de:
- Demanda laboral, empleo, desempleo, empleo formal, capacitación y poder adquisitivo salarial.
- Resultado sanitario, cobertura, capacidad hospitalaria, atención primaria y personal sanitario.
- Educación, pobreza, vivienda y abastecimiento.

La vista completa se pudo abrir en la campaña de prueba y tras la asunción presidencial.

## 2. Políticas públicas y efectos directos
Las doce acciones del módulo están presentes en `actions` y sus botones se habilitan cuando el jugador asume la Presidencia. Antes de la asunción aparecen bloqueadas por el control de autoridad ejecutiva.

| Política | Costo base (M) | Efecto directo |
|---|---:|---|
| Plan educativo | 1.200 | Educación +5; tecnología +1,5 |
| Becas y permanencia educativa | 850 | Educación +2; capacitación +4 |
| Capacitación laboral | 950 | Capacitación +10 |
| Inserción laboral | 900 | Demanda laboral +5; capacitación +2; aprobación +0,5 |
| Incentivo al empleo formal | 500 | Formalidad +1,2; desempleo -0,2; inversión +0,5 |
| Plan de vivienda | 1.900 | Vivienda +8; empleo +0,7 |
| Equipamiento hospitalario | 1.500 | Capacidad hospitalaria +7 |
| Ampliación de cobertura sanitaria | 1.250 | Cobertura sanitaria +7 |
| Formación y contratación sanitaria | 1.150 | Personal sanitario +7; capacitación +1 |
| Atención primaria y prevención | 1.050 | Atención primaria +7; agua +1 |
| Agua y saneamiento | 1.000 | Agua +6; salud +1,5 |
| Abastecimiento básico | 750 | Abastecimiento +5; aprobación +0,5 |

Los costos efectivos se ajustan por inflación mediante `investmentCost()`; por tanto, el importe final puede superar el costo base.

## 3. Consecuencias trimestrales
El cierre trimestral actualiza gradualmente cobertura sanitaria, capacidad hospitalaria, atención primaria, personal sanitario, salud, educación, vivienda, demanda laboral, empleo, pobreza, desempleo y formalidad. La pobreza también responde al empleo, abastecimiento, formalidad, desempleo, salario real y vivienda. No son cambios instantáneos: se acumulan por trimestre.

## 4. Gastos recurrentes
La función `expenses()` incorpora mantenimiento trimestral:
- Salud: capacidad hospitalaria, personal sanitario, atención primaria y cobertura.
- Servicios sociales: educación, capacitación y vivienda.
- Agua: se incluye en el mantenimiento de infraestructura.
Esto evita que ampliar servicios sea una mejora gratuita y permanente. El gasto recurrente aumenta cuando los indicadores superan sus umbrales de referencia.

## 5. Pruebas de navegador realizadas
- La vista Sociedad y las secciones Situación nacional, Economía, Finanzas y deuda, Producción, Provincias, Gobierno, Elecciones e Historial se abrieron correctamente.
- En una campaña aislada, el cierre de un trimestre pasó de Año 1 / T1 a Año 1 / T2 y actualizó tesoro, deuda, indicadores sociales e historial.
- Guardado local: se encontró `rn_save_v1`; tras recargar, el lobby informó la partida guardada en Año 1 / T2.
- Se probó Plan educativo: costo efectivo 1.355 M; educación +5 y tecnología +1,5.
- Se probaron Incentivo al empleo formal, Cobertura sanitaria, Plan de vivienda y Abastecimiento básico; los costos se descontaron y los indicadores directos cambiaron conforme a la definición.
- Se cerró otro trimestre tras esas políticas; se observaron nuevas variaciones sociales y el avance de la historia macroeconómica.
- En una partida de prueba se completó la interna/PASO: la candidatura del jugador ganó por 2 votos de diferencia, quedó habilitada la selección de vicepresidente dentro del partido y pasó a campaña general.
- Se completó el escrutinio general y el balotaje: la fórmula del jugador ganó 51,1% contra 48,9%. Esto valida una victoria, no una derrota.
- Vista previa móvil a 390 px: el ancho del documento no superó el viewport en el lobby (sin desbordamiento horizontal).

## 6. Guardado y preservación
El guardado principal usa `localStorage` con clave `rn_save_v1`; `readSave()` combina el objeto guardado con `defaults()` y normaliza valores numéricos. Las pruebas se hicieron en sesiones aisladas, sin modificar ni importar partidas personales. No se realizó una migración destructiva ni se borró ningún guardado del usuario.

## 7. Pendientes / límites de esta auditoría
1. Falta completar una derrota real en elecciones generales desde navegador y verificar visualmente el estado Oposición y el bloqueo de facultades ejecutivas.
2. Falta probar varios años de gobierno con una batería de políticas sociales, para medir estabilidad de costos, inflación y deuda a largo plazo.
3. Falta validar importación/restauración de una copia exportada en un navegador limpio.
4. El test móvil cubrió el lobby a 390 px; no equivale a una auditoría responsive completa de todas las vistas.
5. No se modificó el código social porque las acciones y efectos revisados funcionaron en las pruebas efectuadas. Cualquier balance numérico debe hacerse con simulaciones prolongadas, no con un solo trimestre.

## 8. Resultado
**Sociedad: funcionamiento básico verificado, balance a largo plazo pendiente.** No se detectó un fallo funcional inmediato en las políticas probadas. La economía conserva costos recurrentes para servicios sociales y sanitarios. Esta auditoría no certifica todavía la estabilidad integral de RN 3.2 ni autoriza publicar en `main`.
