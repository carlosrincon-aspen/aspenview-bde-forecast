# AspenView — Pronóstico Rodante de Ventas y Readiness a 12 Meses
## Guía de Uso

Una plataforma de pronóstico en vivo, basada en navegador, que reemplaza el archivo manual de Excel. Se conecta a Zoho CRM, permite agregar los datos manuales del forecast sobre la información del CRM, y mantiene a todos (gerente + equipo) viendo los mismos números en tiempo real.

---

## 1. Qué hace la plataforma

| Antes (Excel) | Ahora (Plataforma) |
|---|---|
| Deals copiados a mano desde Zoho | Deals traídos automáticamente desde Zoho |
| Cada persona tenía su propio archivo | Todos comparten una vista en vivo (auto-sincronizada) |
| El margen no se medía | Margen integrado, alimenta el flujo de caja |
| Foto estática mensual | Pronóstico rodante de 3 meses que avanza con un clic |
| Sin medida de confiabilidad | Bandas de precisión del pronóstico por horizonte |

**Conviven dos tipos de datos:**
- **De Zoho (automático):** nombre del deal, cuenta/cliente, stage, dueño (BDE), monto, probabilidad, fecha de cierre — se actualizan cada vez que sincronizas.
- **Manuales (los ingresas tú):** headcount mensual por deal, MRR Aureum, ajuste de win-rate, override de margen, las casillas **Forecast** y **Super-Deal**, y la evaluación cualitativa del Super-Deal.

Los datos manuales son los que convierten la información cruda del CRM en un pronóstico real.

---

## 2. Cómo ingresar

1. Abre la URL de la plataforma en Chrome o Edge.
2. Haz clic en **Sign in with Google**.
3. Elige tu cuenta **@aspenview.com**. (Solo se permiten cuentas @aspenview.com.)
4. Caes en la pestaña **Overview**.

**El encabezado (arriba a la derecha) muestra tres cosas:**
- **Sync · Live** (verde) → tus datos están conectados y compartidos. Si dice *Saving…* está guardando un cambio; *Live (updated by …)* significa que un compañero acaba de hacer un cambio que recibiste.
- **Zoho · Connected / Disconnected** → clic para conectar o desconectar Zoho.
- **Tu nombre** y **Log Out**.

> Todo lo que editas se comparte. Si el gerente cambia un número, lo verás aparecer en segundos, y viceversa.

---

## 3. Conectar Zoho (una vez por persona, por sesión)

El token de Zoho dura aproximadamente 1 hora, así que ocasionalmente tendrás que reconectar.

1. En el encabezado, haz clic en la píldora **Zoho · Disconnected**.
2. Te envía a Zoho. Haz clic en **Accept** para autorizar el acceso de lectura a Deals y Accounts.
3. Regresas a la plataforma; la píldora se pone verde: **Zoho · Connected**.

### Traer los deals
1. Ve a la pestaña **Aureum Demand Forecast**.
2. Haz clic en **↻ Sync from Zoho** (arriba a la derecha).
3. Aparece un resumen: cuántos deals se trajeron, cuántos coinciden con los stages permitidos, cuántos se omitieron, y cuántos deals manuales/fantasma se preservan.
4. Haz clic en **Aceptar**. La lista de deals se actualiza con lo último de Zoho.

**Qué deals entran:** solo los deals en estos stages (todo excepto Closed Lost):
`SQL - Solution Discovery · Stage 2 (WTW) · Commercial Design · Recruitment (Path 2.1) · P.S (Path 2.2) · Pricing · Paper Process / Contracting · Closed Won`

**Qué se preserva al re-sincronizar:** tu headcount mensual, MRR Aureum, ediciones de win-rate y las casillas Forecast/Super quedan pegadas a cada deal. Solo los campos de Zoho (stage, monto, dueño, etc.) se refrescan.

---

## 4. Las pestañas, una por una

### 4.1 Overview
Tu foto ejecutiva.
- **Tarjetas KPI superiores:** total de deals, headcount pronosticado (3 meses), revenue ajustado (3 meses), margen $ (3 meses).
- **Selector de Anchor month (arriba a la derecha):** define el mes inicial de la ventana rodante de 3 meses. Avánzalo uno cada mes nuevo (ver §6).
- **Tabla Rolling 3-Month Window:** headcount, revenue proyectado, revenue ajustado y margen $ para el mes ancla y los dos siguientes.
- **Forecast Reliability by Horizon:** las bandas de precisión (M0 / M+1 / M+2). Hasta que se acumule historia, muestran los valores por defecto acordados (80% ±5 / 75% ±10 / 30% ±20).

### 4.2 Aureum Demand Forecast — la lista maestra
Aquí empieza todo. Cada deal en Zoho (en los stages permitidos) es una fila, **agrupada por cuenta** para que todos los deals de un cliente queden juntos.

**Columnas explicadas:**
| Columna | Fuente | ¿Editable? |
|---|---|---|
| **Fcst** (casilla) | Manual | ✅ Marca para incluir este deal en la pestaña **3 mo. revenue** |
| **Super** (casilla) | Manual | ✅ Marca para señalarlo como Super-Deal |
| **BDE** | Dueño de Zoho (o heredado) | Solo lectura. `≈` = inferido de otro deal de la misma cuenta; `⚠ none` = sin dueño en toda la cuenta |
| **Status / Client / Deal / Stage / Loc / 1st Bill.** | Zoho | Solo lectura. Haz clic en el **nombre del deal ↗** para abrirlo directo en Zoho CRM |
| **Columnas mensuales (Jun 26 … Feb 27)** | Manual | ✅ Escribe el headcount que esperas facturable ese mes |
| **MRR Zoho ($)** | `Amount` de Zoho | Solo lectura. Fondo verde = tiene valor y se está usando en el cálculo |
| **MRR Aureum ($)** | Manual | ✅ Tu propio número de MRR. Se usa **solo cuando MRR Zoho está vacío/cero** |
| **Win %** | Probabilidad de Zoho / manual | ✅ Ajusta la probabilidad de cierre usada en el pronóstico |
| **Margin %** | Margin Rules / manual | ✅ Sobrescribe el margen para este deal específico |

**Búsqueda y filtros (arriba):** escribe en la barra de búsqueda para filtrar por deal, cliente o BDE. Usa los dropdowns **BDE / Stage / Status** para selección múltiple (elige dos BDEs a la vez, etc.). Un badge numérico muestra cuántos seleccionaste; "✕ Clear" reinicia un filtro.

### 4.3 3 mo. revenue — el pronóstico rodante
Muestra **solo los deals que marcaste con "Fcst"** en la pestaña Aureum. Para cada uno, a lo largo de la ventana de 3 meses:
- **HC** (headcount ese mes)
- **Proj. Rev** = HC × MRR por cabeza
- **Adj. Rev** = Proj. Rev × Win %
- **Margin $** = Adj. Rev × Margin %

Los totales inferiores y las tres tarjetas resumen (3-mo Projected, Adjusted, Margin) consolidan todo. Filtra por BDE / Stage / Status, y mueve el **Anchor month** para deslizar la ventana.

> Este es el número que ve la empresa. Solo contiene los deals que marcaste deliberadamente — nada se cuela por accidente.

### 4.4 Forecast Accuracy
Mide qué tan confiable ha sido el pronóstico — **no** es una forma de calificar a nadie.
1. La plataforma toma automáticamente una foto (snapshot) del pronóstico rodante la primera vez que se abre cada mes.
2. Cuando un mes cierra, haz clic en **Enter Actuals** y registra el headcount real, revenue ajustado y margen.
3. Las bandas se recalculan: para cada horizonte (mes actual, +1, +2) compara las fotos pasadas contra los reales y muestra **precisión promedio ± desviación estándar**.
4. **Force Snapshot Now** permite tomar una foto cuando quieras.

### 4.5 Super-Deals
La evaluación de programas tier-Aureum — replica la pestaña Super-Deals del Excel. Cada programa (ej. MDP-Zilliant, CGI, MGM) es una fila con dropdowns editables:
- **Deal Confidence, Start Readiness, Readiness Gap, Speed Sensitivity, Ramp Duration, First Phase Timing, Phase Risk Concentration, Program Horizon, Deployment Map.**
- Dropdowns con código de color (verde/ámbar/rojo) para leer de un vistazo.
- **Peak HC / Phase 1 / Activation / Notes:** edita con el botón ✎. Pasa el cursor sobre el ícono ⓘ para ver las notas del programa.
- **+ Add Program** para crear uno nuevo.
- Abajo, un **resumen a nivel deal** suma las finanzas de cada deal que marcaste "Super" en la pestaña Aureum.

### 4.6 Fulfillment View
Headcount por tipo de deal, dividido en Active Roles y Super-Deal Roles — para priorización de delivery/reclutamiento. Filtra por BDE y busca.

### 4.7 MRR Forecast 2026
Revenue ajustado por cliente a lo largo de los 12 meses. Léelo por trimestre leyendo juntas las columnas de los meses correspondientes (ej. Ene–Mar = Q1). Busca por cliente.

### 4.8 Active Customers — Actuals
Solo clientes que actualmente producen revenue (Active / Expanding / Aureum). Muestra headcount actual, MRR por cabeza y $ mensual. Filtra por BDE y status.

### 4.9 Margin Rules
Los supuestos de margen que alimentan el flujo de caja. Una tabla de **Tipo de Deal/Stage → margen % por defecto**, totalmente editable. El margen de cualquier deal se resuelve en este orden:
1. **Override por deal** (el Margin % que escribiste en el deal) →
2. **Regla de Super-Deal** (si está marcado Super) →
3. **Regla por Stage** (coincide con el stage del deal) →
4. **Default** como respaldo.

Revísalas con Finanzas trimestralmente para mantener actualizados los supuestos de flujo de caja.

### 4.10 Settings
- **Zoho integration:** estado, mapeo de campos, conectar/sincronizar/desconectar.
- **Data management:** **Export all data (JSON)** para respaldo, **Import** para restaurar, **Reset all data** para empezar de cero desde la semilla.
- Recordatorio del **flujo de trabajo diario**.

---

## 5. Cómo se mueve realmente el pronóstico (el flujo de datos)

```
ZOHO  ──(Sync)──►  Aureum Demand Forecast
                         │
        ingresas:  HC mensual  +  MRR Aureum (si Zoho vacío)  +  Win %  +  Margin %
        marcas:    Forecast ☑   Super ☑
                         │
                         ▼
   deals Forecast ☑  ──►  3 mo. revenue   (HC × MRR/cabeza × Win% × Margin%)
   deals Super ☑     ──►  resumen Super-Deals
                         │
                         ▼
        KPIs de Overview + bandas de Forecast Accuracy + MRR Forecast
```

**Las palancas que mueves:**
- **HC mensual** → impulsa el revenue proyectado y el pronóstico rodante.
- **MRR Zoho vs MRR Aureum** → el precio efectivo por deal (Zoho gana si tiene valor; si no, tu número de Aureum).
- **Win %** → descuenta el revenue por probabilidad.
- **Margin %** → convierte el revenue en el número de margen que Finanzas necesita.
- **Anchor month** → desliza la ventana de 3 meses hacia adelante.
- **Casillas Forecast / Super** → deciden qué aparece en el pronóstico rodante y en el carril de super-deals.

---

## 6. Ritmo recomendado

| Cuándo | Haz esto | Dónde |
|---|---|---|
| **Cada mañana** | Clic en **↻ Sync from Zoho** para traer cambios de la noche | Aureum Demand Forecast |
| **Diario** | Actualiza HC mensual, MRR Aureum, win % conforme avanzan los deals; marca **Fcst** en deals a pronosticar, **Super** en los tier-Aureum | Aureum Demand Forecast |
| **Semanal** | Revisa los números rodantes de 3 meses | 3 mo. revenue |
| **Mensual (día 1)** | Mueve el **Anchor month** uno hacia adelante — toda la ventana rueda | Selector Anchor (cualquier pestaña) |
| **Mensual (al cerrar un mes)** | **Enter Actuals** para que las bandas de precisión aprendan | Forecast Accuracy |
| **Trimestral** | Revisa **Margin Rules** y las evaluaciones de programas Super-Deal con Finanzas | Margin Rules / Super-Deals |

---

## 7. Tips y solución de problemas

| Síntoma | Solución |
|---|---|
| El revenue muestra $0 | El deal no tiene MRR Zoho ni MRR Aureum. Ingresa un valor en MRR Aureum, o asegúrate de que el Amount esté puesto en Zoho. |
| Un deal no aparece en 3 mo. revenue | Marca la casilla **Fcst** en ese deal en Aureum, y verifica que tenga headcount en la ventana del anchor. |
| El login dice "not authorized" | Usaste una cuenta que no es @aspenview.com. Cambia de cuenta. |
| El sync de Zoho falla (401) | El token de 1 hora venció. Clic en la píldora Zoho → reconecta. |
| El sync trae deals pero el stage se ve mal | El nombre del stage en Zoho no coincide con la lista permitida — repórtalo para agregarlo. |
| Un deal tiene `⚠ none` en BDE | Ningún deal de esa cuenta tiene dueño en Zoho. Asigna un dueño en Zoho y vuelve a sincronizar. |
| Los cambios no aparecen en otro computador | Confirma que ambos muestren **Sync · Live**. Haz hard-refresh (Ctrl+Shift+R). |

---

## 8. Respaldo

El almacenamiento del navegador guarda tus datos localmente y Firestore mantiene la copia compartida. Aun así, exporta un respaldo periódicamente: **Settings → ⬇ Export all data (JSON)**. Para restaurar: **Settings → ⬆ Import data (JSON)**.
