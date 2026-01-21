<!--{"pinCode":false,"dname":"25a378b9-c42b-4d76-b6ce-97f4fba95312","codeMode":"js","hide":true}-->
```js
mutable keylist_flag = 0
```

<!--{"pinCode":false,"dname":"27b9401a-b1f9-4325-9034-9a8227450e17","codeMode":"js","hide":true}-->
```js
keylist = {
  keylist_flag // refresh
  return _.uniq(_.flatten(gxr.nodes().map(n=>Object.keys(n.properties))))
}
```

<!--{"pinCode":false,"dname":"89751a8e-1cef-4b53-a616-bf8514110313","codeMode":"js","hide":true}-->
```js
viewof orientation = Inputs.radio(['up', 'down'], {value:'down', label:'Orientation'})
```

<!--{"pinCode":false,"dname":"84e40639-12f8-45d5-b2f8-e2489851f3ba","codeMode":"js","hide":true}-->
```js
mutable datefield = 'year'
```

<!--{"pinCode":false,"dname":"48688c16-50d8-40e4-b72e-55f63aadfa13","codeMode":"js","hide":true}-->
```js
mutable yfield = 'riskLabel'
```

<!--{"pinCode":false,"dname":"bd74c26b-31ca-4e9e-83b5-f915dceed74a","codeMode":"js","hide":true}-->
```js
// ============================================================================
// DARK THEME CONFIGURATION - Neutral Grayscale
// Design Principle: Color encodes meaning in the graph; the agent panel stays 
// neutral so attention always flows to the data.
// ============================================================================

theme_dark = ({
  // Color Scheme - Neutral Grayscale (Warm-neutral tones, no blue tint)
  colors: {
    // Background colors (Panel layers - warm-neutral grays, no blue tint)
    bgPrimary: '#1C1C1C',        // Panel base - main container (neutral gray)
    bgSecondary: '#242424',      // Inner card - question/config block (neutral gray)
    bgCard: '#242424',           // Card background (neutral gray)
    bgCardLight: '#272727',      // Light card variant (neutral gray)
    bgInput: '#1F1F1F',          // Input fields - text input area (neutral gray)
    
    // Border colors (Subtle separation - warm-neutral)
    borderPrimary: '#2E2E2E',    // Divider / border (neutral gray)
    borderSecondary: '#2E2E2E',  // Secondary border (neutral gray)
    borderTertiary: '#2E2E2E',  // Tertiary border (neutral gray)
    borderFocus: 'rgba(127, 163, 200, 0.4)', // Focus ring - more subtle accent (reduced opacity)
    
    // Text colors (Hierarchy - softer contrast)
    textPrimary: '#B8BDC5',      // Body text (lighter for less contrast)
    textSecondary: '#D8DCE2',    // Panel title (softer, less stark)
    textTertiary: '#9CA3AD',     // Section headers (lighter)
    textAccent: '#B8BDC5',       // Accent text (neutral)
    textPlaceholder: '#6F7680',  // Placeholder text
    textDisabled: '#4A4F57',     // Disabled text
    
    // Button colors (Grayscale - warm-neutral)
    btnPrimary: '#2E2E2E',       // Primary button default (neutral gray)
    btnPrimaryHover: '#373737',  // Primary button hover (neutral gray)
    btnPrimaryActive: '#404040', // Primary button active (neutral gray)
    btnPrimaryGradient: 'linear-gradient(135deg, #2E2E2E 0%, #373737 100%)',
    btnSecondary: 'transparent', // Secondary button
    btnSecondaryHover: 'rgba(46, 46, 46, 0.2)', // Secondary hover (neutral)
    
    // Toggle colors (Brightness-based - warm-neutral)
    toggleOff: '#404040',        // Toggle OFF state (neutral gray)
    toggleOn: '#7A7A7A',         // Toggle ON state (neutral gray)
    
    // Shadow colors (Neutral)
    shadowPrimary: 'rgba(0,0,0,0.4)',
    shadowButton: 'rgba(0,0,0,0.2)',
    shadowButtonHover: 'rgba(0,0,0,0.3)',
  },
  
  // Spacing
  spacing: {
    cardMargin: '20px 0',
    cardPadding: '24px',
    sectionPadding: '20px',
    sectionMargin: '24px',
    inputPadding: '10px 12px',
    buttonPadding: '14px',
    buttonPaddingSmall: '8px 16px',
    gapSmall: '10px',
    gapMedium: '12px',
    gapLarge: '20px',
  },
  
  // Typography
  typography: {
    fontFamily: "'Segoe UI', Tahoma, Geneva, Verdana, sans-serif",
    fontSizeHeading: '1.8em',
    fontSizeSubheading: '1.2em',
    fontSizeBody: '0.95em',
    fontSizeSmall: '0.9em',
    fontSizeTiny: '0.85em',
    fontSizeMicro: '0.8em',
    fontWeightBold: 'bold',
    fontWeightSemiBold: '600',
  },
  
  // Border radius (Subtle rounding to match UI chrome - 4-6px range)
  borderRadius: {
    card: '6px',        // Main panel (matches file tab ~4-6px)
    section: '4px',     // Inner sections
    input: '4px',       // Input fields
    button: '4px',      // Buttons (matches toolbar buttons ~2-4px)
    buttonSmall: '4px', // Small buttons
  },
  
  // Shadows (Neutral - lighter for less weight)
  shadows: {
    card: '0 2px 12px rgba(0,0,0,0.2)',
    button: '0 2px 8px rgba(0,0,0,0.15)',
    buttonHover: '0 4px 12px rgba(0,0,0,0.2)',
  },
  
  // Transitions
  transitions: {
    default: '0.2s',
    transform: 'transform 0.2s, box-shadow 0.2s',
    background: 'background 0.2s',
    border: 'border-color 0.2s',
    button: 'background 0.2s, color 0.2s',
  },
  
  // Gradients (Neutral grayscale - warm-neutral, no blue tint)
  gradients: {
    cardBackground: 'linear-gradient(135deg, #1C1C1C 0%, #242424 100%)',
    buttonPrimary: 'linear-gradient(135deg, #2E2E2E 0%, #373737 100%)',
  }
})
```

<!--{"pinCode":false,"dname":"5b1ea9bf-372c-4247-b6b6-5e2291e5ef13","codeMode":"js","hide":true}-->
```js
// ============================================================================
// LIGHT THEME CONFIGURATION - Neutral Grayscale
// Design Principle: Color encodes meaning in the graph; the agent panel stays 
// neutral so attention always flows to the data.
// ============================================================================

theme_light = ({
  // Color Scheme - Neutral Grayscale (Light Mode)
  colors: {
    // Background colors (Panel layers - light)
    bgPrimary: '#F8F9FA',        // Panel base - main container
    bgSecondary: '#FFFFFF',      // Inner card - question/config block
    bgCard: '#FFFFFF',          // Card background
    bgCardLight: '#F8F9FA',     // Light card variant
    bgInput: '#FFFFFF',         // Input fields - text input area
    
    // Border colors (Subtle separation - light)
    borderPrimary: '#E1E4E8',   // Divider / border
    borderSecondary: '#E1E4E8', // Secondary border
    borderTertiary: '#E1E4E8',  // Tertiary border
    borderFocus: 'rgba(127, 163, 200, 0.5)', // Focus ring - subtle accent (#7FA3C8 at 50%)
    
    // Text colors (Hierarchy - light mode)
    textPrimary: '#24292E',     // Body text
    textSecondary: '#1A1F24',   // Panel title
    textTertiary: '#586069',    // Section headers
    textAccent: '#24292E',      // Accent text (neutral)
    textPlaceholder: '#959DA5', // Placeholder text
    textDisabled: '#C6CBD1',    // Disabled text
    
    // Button colors (Grayscale with brightness variation - light)
    btnPrimary: '#F6F8FA',      // Primary button default
    btnPrimaryHover: '#E1E4E8', // Primary button hover
    btnPrimaryActive: '#D1D5DA', // Primary button active
    btnPrimaryGradient: 'linear-gradient(135deg, #F6F8FA 0%, #E1E4E8 100%)',
    btnSecondary: 'transparent', // Secondary button
    btnSecondaryHover: 'rgba(246, 248, 250, 0.5)', // Secondary hover
    
    // Toggle colors (Brightness-based, not color - light)
    toggleOff: '#D1D5DA',       // Toggle OFF state
    toggleOn: '#959DA5',        // Toggle ON state
    
    // Shadow colors (Neutral - light)
    shadowPrimary: 'rgba(0,0,0,0.1)',
    shadowButton: 'rgba(0,0,0,0.08)',
    shadowButtonHover: 'rgba(0,0,0,0.12)',
  },
  
  // Spacing (same as dark theme)
  spacing: {
    cardMargin: '20px 0',
    cardPadding: '24px',
    sectionPadding: '20px',
    sectionMargin: '24px',
    inputPadding: '10px 12px',
    buttonPadding: '14px',
    buttonPaddingSmall: '8px 16px',
    gapSmall: '10px',
    gapMedium: '12px',
    gapLarge: '20px',
  },
  
  // Typography (same as dark theme)
  typography: {
    fontFamily: "'Segoe UI', Tahoma, Geneva, Verdana, sans-serif",
    fontSizeHeading: '1.8em',
    fontSizeSubheading: '1.2em',
    fontSizeBody: '0.95em',
    fontSizeSmall: '0.9em',
    fontSizeTiny: '0.85em',
    fontSizeMicro: '0.8em',
    fontWeightBold: 'bold',
    fontWeightSemiBold: '600',
  },
  
  // Border radius (Subtle rounding to match UI chrome - 4-6px range)
  borderRadius: {
    card: '6px',        // Main panel (matches file tab ~4-6px)
    section: '4px',     // Inner sections
    input: '4px',       // Input fields
    button: '4px',      // Buttons (matches toolbar buttons ~2-4px)
    buttonSmall: '4px', // Small buttons
  },
  
  // Shadows (Neutral - light)
  shadows: {
    card: '0 4px 20px rgba(0,0,0,0.1)',
    button: '0 4px 15px rgba(0,0,0,0.08)',
    buttonHover: '0 6px 20px rgba(0,0,0,0.12)',
  },
  
  // Transitions (same as dark theme)
  transitions: {
    default: '0.2s',
    transform: 'transform 0.2s, box-shadow 0.2s',
    background: 'background 0.2s',
    border: 'border-color 0.2s',
    button: 'background 0.2s, color 0.2s',
  },
  
  // Gradients (Neutral grayscale - light)
  gradients: {
    cardBackground: 'linear-gradient(135deg, #F8F9FA 0%, #FFFFFF 100%)',
    buttonPrimary: 'linear-gradient(135deg, #F6F8FA 0%, #E1E4E8 100%)',
  }
})
```

<!--{"pinCode":false,"dname":"eecdc065-f925-4dc4-8d15-dfaf8d2550d0","codeMode":"js","hide":true}-->
```js
mutable selectedTheme = 'dark'
```

<!--{"pinCode":false,"dname":"5e88aba8-1353-4d91-94c7-8b76af394941","codeMode":"js","hide":true}-->
```js
// Active theme based on selection
theme = selectedTheme === 'dark' ? theme_dark : theme_light
```

<!--{"pinCode":false,"dname":"6a6c7180-0165-4b0f-b1f8-508b11522f64","codeMode":"js"}-->
```js
{
  const c = theme.colors;
  const s = theme.spacing;
  const t = theme.typography;
  const r = theme.borderRadius;
  const sh = theme.shadows;
  const tr = theme.transitions;
  const g = theme.gradients;
  
  const container = html`
<div id="timeline-manager-card" style="
  margin: ${s.cardMargin};
  padding: ${s.cardPadding};
  background: ${g.cardBackground};
  border-radius: ${r.card};
  border: 2px solid ${c.borderPrimary};
  box-shadow: ${sh.card};
  font-family: ${t.fontFamily};
  width: 100%;
  box-sizing: border-box;
  color: ${c.textPrimary};
">
  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: ${s.gapLarge};">
    <h2 style="margin: 0; font-size: ${t.fontSizeHeading}; color: ${c.textSecondary}; display: flex; align-items: center; gap: ${s.gapMedium};">
      📅 Timeline Plot Manager
    </h2>
  </div>
  
  <!-- Configuration Section -->
  <div style="margin-bottom: ${s.sectionMargin}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: ${s.gapLarge};">
       <h3 style="margin: 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">⚙️ Configuration</h3>
       <button id="refresh-props-btn" style="padding: ${s.buttonPaddingSmall}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.buttonSmall}; cursor: pointer; font-weight: ${t.fontWeightBold}; font-size: ${t.fontSizeTiny}; transition: ${tr.background};" onmouseover="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'">🔄 Refresh Properties</button>
    </div>
    
    <div style="margin-bottom: ${s.gapLarge};">
      <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; display: block; font-size: ${t.fontSizeSmall};">Date Field Name</label>
      <input id="datefield-input" type="text" value="${datefield}" style="background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; padding: ${s.inputPadding}; width: 100%; box-sizing: border-box; font-size: ${t.fontSizeBody}; outline: none; transition: ${tr.border};" placeholder="Type date field name (e.g., 'year')..." onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'" />
      <div style="color: ${c.textTertiary}; font-size: ${t.fontSizeMicro}; margin-top: 6px; font-style: italic;">Date should be in ISO format</div>
    </div>
    
    <div style="margin-bottom: ${s.gapLarge};">
      <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; display: block; font-size: ${t.fontSizeSmall};">Y Field Name</label>
      <div style="display: flex; gap: ${s.gapSmall};">
        <select id="yfield-select" style="flex: 1; background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; padding: ${s.inputPadding}; outline: none; transition: ${tr.border}; cursor: pointer;" onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'">
          ${keylist.map(k => html`<option value="${k}" ${k === yfield ? 'selected' : ''}>${k}</option>`)}
        </select>
      </div>
    </div>
  </div>
  
  <!-- Timeline Actions Section -->
  <div style="margin-bottom: ${s.sectionMargin}; padding: ${s.sectionPadding}; background: ${c.bgCardLight}; border-radius: ${r.section}; border: 1px solid ${c.borderTertiary};">
     <h3 style="margin: 0 0 ${s.gapLarge} 0; color: ${c.textSecondary}; font-size: ${t.fontSizeSubheading};">⚡ Timeline Actions</h3>
     
     <div style="display: flex; gap: ${s.gapMedium}; flex-wrap: wrap;">
       <button id="plot-timeline-btn" style="flex: 1; min-width: 200px; padding: ${s.buttonPadding}; background: ${g.buttonPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.button}; font-size: 1.1em; font-weight: ${t.fontWeightBold}; cursor: pointer; box-shadow: ${sh.button}; transition: ${tr.transform};" onmouseover="this.style.transform='translateY(-2px)'; this.style.boxShadow='${sh.buttonHover}'" onmouseout="this.style.transform='translateY(0)'; this.style.boxShadow='${sh.button}'">
         📊 Plot Timeline
       </button>
       <button id="link-market-btn" style="flex: 1; min-width: 200px; padding: ${s.buttonPadding}; background: ${c.btnSecondary}; color: ${c.textAccent}; border: 2px solid ${c.textAccent}; border-radius: ${r.button}; font-size: 1.1em; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.button};" onmouseover="this.style.background='${c.btnSecondaryHover}'; this.style.color='${c.textAccent}'" onmouseout="this.style.background='${c.btnSecondary}'; this.style.color='${c.textAccent}'">
         🔗 Link Market
       </button>
     </div>
  </div>
</div>`;

  // Event listeners
  container.querySelector('#refresh-props-btn').onclick = () => {
    mutable keylist_flag += 1;
    gxr.toast().info("Refreshing properties...");
    // Update the select options after refresh
    setTimeout(() => {
      const select = container.querySelector('#yfield-select');
      const currentValue = select.value;
      select.innerHTML = keylist.map(k => `<option value="${k}" ${k === currentValue ? 'selected' : ''}>${k}</option>`).join('');
    }, 100);
  };

  container.querySelector('#datefield-input').oninput = (e) => {
    mutable datefield = e.target.value;
  };

  container.querySelector('#yfield-select').onchange = (e) => {
    mutable yfield = e.target.value;
  };

  container.querySelector('#plot-timeline-btn').onclick = async () => {
    gxr.toast().info("Plotting timeline...");
    try {
      await plotTimeline(datefield, yfield);
      gxr.toast().success("Timeline plotted successfully.");
    } catch (e) {
      gxr.toast().error(e.message || "Error plotting timeline.");
    }
  };

  container.querySelector('#link-market-btn').onclick = () => {
    try {
      linkMarket();
      gxr.toast().success("Market links created.");
    } catch (e) {
      gxr.toast().error(e.message || "Error linking market.");
    }
  };

  return container;
}
```

<!--{"pinCode":false,"dname":"186d20d9-2b1a-4ed5-9d0d-e1ccc3fdab94","codeMode":"js","hide":true}-->
```js
// Inputs.button('Canonicalize Market', {reduce: async ()=>{
//   simplifyMarket()
//   await gxr.sleep(300)
  
//   gxr.merge({
//     category: "Market",
//     keys: [ 'marketKey', 'year']
//   })

// }})
```

<!--{"pinCode":false,"dname":"2e3c10a3-c496-4ec3-b619-6aa731328b51","codeMode":"js","hide":true}-->
```js
plotTimeline = async (datefield, yfield)=>{ 
  let max_y= 0.7
  let min_y=-0.7
  let y_spread = max_y - min_y
  let x_spread = 2*y_spread

  await createDateNodes()
  
//   use __gxr_date__ field so we can plot timeline in both directions
  gxr.nodes()
    .filter(n=>n.properties[datefield])
    .forEach(n=>n.properties.__gxr_date__ = Date.parse(n.properties[datefield]) * (orientation == 'up' ? -1 : 1))
  
  await gxr.sleep(300)
  gxr.nodes()
    .forEach(n=>{
      n.position.x = 0
      n.position.y = 0
  })
  await gxr.sleep(300)

  gxr.nodes().distributionBy({
    dimension: 'x',
    bin: datefield,
    binType: 'date',
    spread: x_spread,
  })

  await gxr.sleep(200)
  gxr.nodes().distributionBy({
    dimension: 'y',
    bin: yfield,
//     bin: datefield,
    binType: 'categorical',
    reverse: true,
    spread: y_spread,
  })
  
  
//   gxr.nodes().distributionBy({
//     dimension: 'y',
//     bin: '__gxr_date__',
// //     bin: datefield,
// //     binType: 'date',
//     reverse: true,
//     spread: y_spread,
//   })
//   await gxr.sleep(200)
//   gxr.nodes().distributionBy({
//     dimension: 'y',
//     spread: y_spread,
//   })

  await gxr.sleep(200)

  gxr.nodes({category:'Date'})
    .filter(n=>n.properties.location==1)
    .forEach(n=>n.position.y = max_y+0.07)
  
  gxr.nodes({category:'Date'})
    .filter(n=>n.properties.location==-1)
    .forEach(n=>n.position.y = min_y-0.05)  
  
  arrangeMarketNodes()
  
  await gxr.sleep(300)
  gxr.nodes()
    .filter(n=>n.properties.__gxr_date__)
    .forEach(n=>{delete n.properties.__gxr_date__})

}
```

<!--{"pinCode":false,"dname":"994a7331-83de-4b59-84ec-fd1fc01993ea","codeMode":"js","hide":true}-->
```js
arrangeMarketNodes = ()=>{
  let nodes = gxr.edges(':ENTERING').map(n=>n.target)
  nodes.forEach(n=>{
    n.position.x = n.position.x-0.1
  })
  nodes = gxr.edges(':EXITING').map(n=>n.target)
  nodes.forEach(n=>{
    n.position.x = n.position.x+0.1
  })
  
  
}
```

<!--{"pinCode":false,"dname":"8b104d26-2605-4ea8-b769-3a8882ccfd92","codeMode":"js","hide":true}-->
```js
linkMarket = ()=>{
  gxr.link({
    sourceCategory: "Market",
    targetCategory: "Market",
    sourceProperty: yfield,
    targetProperty: yfield,
    relationship: "SAME",
    match: {
      type: "case-insensitive"
    }
  })
}
```

<!--{"pinCode":false,"dname":"cffa6d29-0dc0-4e28-ad9f-69e5e32fd25a","codeMode":"js","hide":true}-->
```js
getDateStrings = (raw_dates, max_ticks = 10, opts = {}) => {
  if (!raw_dates || !raw_dates.length) return [];

  const { mode = "auto" } = opts; // "auto" | "year"

  // --- helpers -------------------------------------------------
  const normalizeIso = s =>
    typeof s === "string" ? s.replace(/(\.\d{3})\d+$/, "$1") : s;

  const isYear = v =>
    (typeof v === "number" && Number.isInteger(v) && v >= 1000 && v <= 3000) ||
    (typeof v === "string" &&
      /^\s*\d{4}\s*$/.test(v) &&
      +v.trim() >= 1000 &&
      +v.trim() <= 3000);

  const toDate = v => {
    if (v instanceof Date) return v;

    // Year input: Jan 1 of that year in UTC (avoids 1969/1970 timezone edge cases)
    if (isYear(v)) {
      const y = +String(v).trim();
      return new Date(Date.UTC(y, 0, 1));
    }

    // ISO strings (trim + microseconds normalization)
    const s = typeof v === "string" ? v.trim() : v;
    return new Date(normalizeIso(s));
  };
  // ------------------------------------------------------------

  const parsed = raw_dates.map(toDate).filter(d => !isNaN(d));
  if (!parsed.length) return [];

  const min = new Date(Math.min(...parsed.map(d => +d)));
  const max = new Date(Math.max(...parsed.map(d => +d)));

  // formatters (local)
  const pad = n => String(n).padStart(2, "0");
  const formatDate = d =>
    `${d.getFullYear()}-${pad(d.getMonth() + 1)}-${pad(d.getDate())}`;
  const formatDateTime = d =>
    `${formatDate(d)}T${pad(d.getHours())}:${pad(d.getMinutes())}`;

  // yearly formatter (UTC)
  const formatYear = d => String(d.getUTCFullYear());

  const yearOnlyInput = raw_dates.every(isYear);
  const useYearly = mode === "year" || (mode === "auto" && yearOnlyInput);

  // single value
  if (+min === +max) {
    return [useYearly ? String(min.getUTCFullYear()) : formatDateTime(min)];
  }

  let interval, totalUnits, formatter;

  if (useYearly) {
    // ✅ UTC-aligned year ticks to match Date.UTC(...) year parsing
    interval = d3.utcYear;
    totalUnits = Math.max(1, max.getUTCFullYear() - min.getUTCFullYear());
    formatter = formatYear;
  } else {
    const diffMs = max - min;
    const diffH = diffMs / 36e5;
    const diffD = diffH / 24;

    if (diffD >= 3) {
      interval = d3.timeDay;
      totalUnits = Math.max(1, Math.round(diffMs / 86400000));
      formatter = formatDate;
    } else if (diffH >= 3) {
      interval = d3.timeHour;
      totalUnits = Math.max(1, Math.round(diffMs / 36e5));
      formatter = formatDateTime;
    } else {
      interval = d3.timeMinute;
      totalUnits = Math.max(1, Math.round(diffMs / 60000));
      formatter = formatDateTime;
    }
  }

  const step = Math.max(1, Math.ceil(totalUnits / max_ticks));
  const it = interval.every(step);

  let ticks = it.range(interval.floor(min), max);

  if (!ticks.length) {
    ticks = [interval.floor(min), interval.floor(max)];
  } else {
    const last = interval.floor(max);
    if (+ticks[ticks.length - 1] !== +last) ticks.push(last);
  }

  if (ticks.length > max_ticks) {
    const stride = Math.ceil(ticks.length / max_ticks);
    ticks = ticks.filter((_, i) => i % stride === 0);
    ticks[ticks.length - 1] = interval.floor(max);
  }

  return ticks.map(formatter);
};
```

<!--{"pinCode":false,"dname":"e30e4861-90dd-46f2-a976-c813ba05366d","codeMode":"js","hide":true}-->
```js
getDateStringsOld = (raw_dates, max_ticks = 10) => {
  if (!raw_dates || !raw_dates.length) return [];

  // Accept Date or ISO string with microseconds
  const normalizeIso = s => typeof s === "string"
    ? s.replace(/(\.\d{3})\d+$/, "$1")
    : s;
  const toDate = v => v instanceof Date ? v : new Date(normalizeIso(v));
  const parsed = raw_dates.map(toDate).filter(d => !isNaN(d));
  if (!parsed.length) return [];

  const min = new Date(Math.min(...parsed.map(d => +d)));
  const max = new Date(Math.max(...parsed.map(d => +d)));
  if (+min === +max) return [formatDateTime(min)]; // single point

  const diffMs = max - min;
  const diffH = diffMs / 36e5;
  const diffD = diffH / 24;

  // Formatters
  function pad(n){ return String(n).padStart(2,"0"); }
  function formatDate(d){
    return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`;
  }
  function formatDateTime(d){
    return `${formatDate(d)}T${pad(d.getHours())}:${pad(d.getMinutes())}`;
  }

  // Choose interval & formatter
  let interval, totalUnits, useDateOnly;
  if (diffD >= 3) {
    // Many days: date-only labels, day-based ticks
    useDateOnly = true;
    interval = d3.timeDay;
    totalUnits = Math.max(1, Math.round(diffMs / 86400000)); // days
  } else if (diffH >= 3) {
    // < 3 days: hour ticks, show time
    useDateOnly = false;
    interval = d3.timeHour;
    totalUnits = Math.max(1, Math.round(diffMs / 36e5)); // hours
  } else {
    // < 3 hours: minute ticks, show time
    useDateOnly = false;
    interval = d3.timeMinute;
    totalUnits = Math.max(1, Math.round(diffMs / 60000)); // minutes
  }

  // Pick a step so we aim for <= max_ticks
  const step = Math.max(1, Math.ceil(totalUnits / max_ticks));
  const it = interval.every(step);

  // Build ticks aligned to interval boundaries
  let ticks = it.range(interval.floor(min), max);

  // Ensure we have at least one tick and include the end if aligned
  if (!ticks.length) ticks = [interval.floor(min), interval.floor(max)];
  else {
    const last = interval.floor(max);
    if (+ticks[ticks.length - 1] !== +last && +last > +ticks[ticks.length - 1]) {
      ticks.push(last);
    }
  }

  // Hard cap to max_ticks (downsample)
  if (ticks.length > max_ticks) {
    const stride = Math.ceil(ticks.length / max_ticks);
    ticks = ticks.filter((_, i) => i % stride === 0);
    // make sure last tick aligns with end
    const last = interval.floor(max);
    if (+ticks[ticks.length - 1] !== +last) {
      ticks[ticks.length - 1] = last;
    }
  }

  return ticks.map(useDateOnly ? formatDate : formatDateTime);
};
```

<!--{"pinCode":false,"dname":"18f32932-5608-44f6-8157-c67814c7d85a","codeMode":"js","hide":true}-->
```js
createDateNodes = async ()=>
{
//   remove existing Date nodes()
  gxr.nodes({category:'Date'}).remove()
  await gxr.sleep(500)
  
  let raw_dates = gxr.nodes()
    .filter(n=>n.properties[datefield])
    .map(n=>n.properties[datefield])

  const dateStrings = getDateStrings(raw_dates)
//   return dateStrings

  let nodes_data_up= dateStrings.map(d=>{
    let prop = {}; prop[datefield]=d;
    return {id: d+'-up', category:'Date', properties:{...prop, location: 1}}
  })
  let nodes_data_down= dateStrings.map(d=>{
    let prop = {}; prop[datefield]=d;
    return {id: d+'-down', category:'Date', properties:{...prop, location: -1}}
  })

  let nodes_data = _.flatten([nodes_data_up, nodes_data_down])
  
  gxr.add(nodes_data)
  
  let edge_list = dateStrings.map(d=>{
    return {sourceId: d+'-down', targetId: d+'-up'}
  })
  gxr.add(edge_list)
  gxr.dispatchGraphDataUpdate();
  return edge_list
  
}
```

<!--{"pinCode":false,"dname":"b181e914-d82e-4777-8a7b-ac6d65fa1cdf","codeMode":"js","hide":true}-->
```js
md `### Appendix`
```

<!--{"pinCode":false,"dname":"43c6efac-6f9b-436c-84eb-72f65cb14761","codeMode":"js","hide":true}-->
```js
API=_app.controller.API
```

<!--{"pinCode":false,"dname":"2482842d-c4db-4a55-963b-e62ba3f8b10d","codeMode":"js","hide":true}-->
```js
function debounce(input, delay = 1000) {
  return Generators.observe(notify => {
    let timer = null;
    let value;

    // On input, check if we recently reported a value.
    // If we did, do nothing and wait for a delay;
    // otherwise, report the current value and set a timeout.
    function inputted() {
      if (timer !== null) return;
      notify(value = input.value);
      timer = setTimeout(delayed, delay);
    }

    // After a delay, check if the last-reported value is the current value.
    // If it’s not, report the new value.
    function delayed() {
      timer = null;
      if (value === input.value) return;
      notify(value = input.value);
    }

    input.addEventListener("input", inputted), inputted();
    return () => input.removeEventListener("input", inputted);
  });
}
```

<!--{"pinCode":false,"dname":"6055d289-774e-4b43-a367-6c4bbedbd9c2","codeMode":"js","hide":true}-->
```js
import {simplifyMarket} from 'backup/map_market.md'
```

```

```
