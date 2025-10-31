# Informe de Cumplimiento – Posgrados U. de La Sabana (AS-IS → TO-BE)

## 1. Resumen ejecutivo
Se evaluó el cumplimiento normativo del sistema del cliente (Posgrados) en **tres procesos**: (P1) verificación de documentos, (P2) envío de recibo y cobro, y (P3) datos regulatorios. El análisis se centró en el **estado actual (AS-IS)** con evidencias observadas y en brechas priorizadas, proponiendo mejoras **TO-BE** ya diseñadas en la arquitectura (automatización ligera con SIGA como caja negra y M365).

**Resultado global (AS-IS):**
- Fortalezas: recolección mínima en P1, uso de pasarela externa en P2, prácticas de versionado básico en documentos.
- Brechas clave: **falta de validación automática de adjuntos (P1)**, **ausencia de recordatorios programados y confirmación por webhook (P2)**, **no existe fuente única oficial en SharePoint para regulatorio (P3)**, y **controles transversales** (aviso de privacidad explícito, retención/purga, RBAC revisado, DPA con terceros, pruebas de restauración).

## 2. Alcance y contexto
- **Alcance**: Procesos P1, P2, P3 y controles transversales que afecten confidencialidad, integridad y disponibilidad.
- **Suposiciones**: SIGA se trata como contenedor único (caja negra). M365 disponible (SharePoint/Power Automate/Exchange). Pasarela institucional existente.
- **No incluido**: cambios estructurales en SIGA, rediseño de contratos base de la Universidad.

## 3. Marco normativo aplicado
- **Ley 1581 de 2012 (Colombia)** y lineamientos de la SIC.
- **ISO/IEC 27001:2022** (controles de gobierno, acceso, operación y continuidad).
- **Buenas prácticas PCI-DSS** (por uso de pasarela externa; no se almacenan PAN).
- **Contratos/DPA con terceros** (M365/pasarela) y políticas institucionales vigentes.

> Este informe no sustituye asesoría legal. Las recomendaciones se alinean a buenas prácticas y normativa aplicable.

## 4. Metodología
- Checklist AS-IS con campos: requisito, norma/marco, evidencia, estado, riesgo, dueño, prioridad y acción correctiva.
- Escala: **Sí / Parcial / No / N.A.** (foto del presente). Priorización por impacto y factibilidad.
- Evidencia: diagramas AS-IS, descripción operativa, y prácticas documentadas en el área.

## 5. Resultados por proceso (AS-IS)
### P1 – Verificación de documentos
- **Sí**: Minimización de datos (solo 3 adjuntos).
- **Parcial**: Aviso de privacidad visible; trazabilidad de adjuntos.
- **No**: Validación automática (tipo/tamaño/“en blanco”/antimalware/OCR); retención/purga de rechazados.
- **Riesgo**: Alto por posibilidad de archivos inválidos o maliciosos y retención indefinida.

**Acciones (TO-BE) sugeridas**: validador automático previo a persistencia; job de purga; reporte de auditoría por expediente; aviso de privacidad en el punto de captura.

### P2 – Envío de recibo y cobro
- **Parcial**: Mensajería operativa; no se almacenan datos de tarjeta (pasarela).
- **No**: Programación T-7/T+0/T+3; webhook firmado e idempotente para marcar pagos y detener recordatorios.
- **Riesgo**: Medio–Alto por reprocesos y errores de estado de pago.

**Acciones (TO-BE) sugeridas**: scheduler de recordatorios y generación de PDF; integración de webhook con firma/clave e idempotencia; plantilla de correo con referencia a privacidad y canal ARCO.

### P3 – Datos regulatorios
- **No**: Fuente única en SharePoint List; hash de detección de cambios; UPSERT; notificación de cambios.
- **Parcial**: Versionado básico si el Excel está en OneDrive/SharePoint.
- **Riesgo**: Medio por inconsistencias, desactualización y trazabilidad limitada.

**Acciones (TO-BE) sugeridas**: Lista SharePoint “ProgramaGobiernoInfo”, Flow de ingesta con comparación por hash y UPSERT mínimo (SNIES/Resolución/Fecha/URL/LastUpdated); consumo en solo-lectura por CMS/áreas; alertas de cambio/error.

## 6. Controles transversales
- **Parcial**: Política de privacidad publicada, pero sin enlace directo en formularios.
- **Parcial**: RBAC sin revisión periódica formal; incidentes sin simulacro específico del proceso.
- **Parcial**: DPA/contratos con pasarela no verificados para este proceso concreto.
- **Parcial**: Backups con evidencia de restore no documentada.
- **Riesgo**: Medio.

**Acciones (TO-BE) sugeridas**: enlace a política/aviso en formularios; revisión trimestral de permisos; simulacro anual de incidentes y registro; verificación DPA/contratos; prueba de restauración y acta.

## 7. Priorización (Roadmap 0–90 días)
- **0–30 días (Quick Wins)**: Aviso de privacidad en formulario P1; plantilla de correo P2; crear SharePoint List P3; definir RBAC y vistas de solo-lectura; programación de prueba de restore.
- **31–60 días**: Validador automático de adjuntos (tipo/tamaño/“en blanco”); scheduler T-7/T+0/T+3 con registro; UPSERT y `LastUpdated` en P3.
- **61–90 días**: Webhook firmado e idempotente; job de purga de adjuntos rechazados; simulacro de incidentes; verificación contractual (DPA).

## 8. Métricas (KPIs sugeridos)
- **P1**: % adjuntos rechazados automáticamente; tiempo medio de validación; nº auditorías por mes.
- **P2**: Tasa de pago antes de T+0; % pagos confirmados por webhook; reducción de correos manuales.
- **P3**: Latencia de actualización tras cambio en fuente; nº inconsistencias detectadas; disponibilidad de la lista.
- **Transversal**: % permisos revisados y cerrados; tiempo a restaurar (restore test).

## 9. Conclusión
El estado **AS-IS** muestra oportunidades claras de mejora que el **TO-BE** propuesto cubre con cambios de baja complejidad y alto impacto (automatización de validaciones, recordatorios, fuente única y trazabilidad). Ejecutando el roadmap 0–90 días, el cliente puede alcanzar un cumplimiento alto, reducir riesgo operativo y mejorar la experiencia de usuario y áreas internas.
