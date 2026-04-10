<!--{"pinCode":false,"dname":"e71305d0-3924-444c-8efd-c40a1054ac68","codeMode":"js","hide":true}-->
```js
mutable refreh_flag = 0
```

<!--{"pinCode":true,"dname":"798ffdac-18ad-43d1-a6d7-701257ec2f17","codeMode":"js","hide":true}-->
```js
db = {
  const gvx = window.top?.globalVariable ?? window.parent?.globalVariable;
  return gvx?.project?.databaseType ?? "unknown";
}
```

<!--{"pinCode":false,"dname":"f11a70f7-ef8a-48e8-ba81-091e3ff8acc4","codeMode":"js","hide":true}-->
```js
company_data = {
  let a = refreh_flag
  const bq = String(db || "").toLowerCase() === "bigquerygraph";
  const companyListCypher = bq
    ? `MATCH (c:Company)
return distinct(c.id) as ticker, c.label as name, c.sic as sic`
    : `MATCH (c:Company)
RETURN DISTINCT c.id AS ticker, c.label AS name, c.sic AS sic`;
  try {
    let data = await BQGQueryToTable(companyListCypher);
    return data || [];
  } catch (e) {
    console.error("Error loading company data:", e);
    return [];
  }
}
```

<!--{"pinCode":false,"dname":"bec4590b-8c06-4753-b1b7-ebea9887ed9b","codeMode":"js","hide":true}-->
```js
mutable selected_tickers = new Set();
```

<!--{"pinCode":false,"dname":"822a8d82-5063-414d-b5b0-32174dfc00df","codeMode":"js","hide":true}-->
```js
mutable analysis_type = "Market"
```

<!--{"pinCode":false,"dname":"1767da56-fa01-476f-afd4-2605f18eacd0","codeMode":"js","hide":true}-->
```js
mutable selected_extract_prop = ""
```

<!--{"pinCode":false,"dname":"69fcd094-4783-431c-86cc-1a20234fd6a0","codeMode":"js","hide":true}-->
```js
prop_list = {
  const type = analysis_type;
  const nodes = gxr.nodes({category: type}).slice(0, 50).toArray();
  let keys = [];
  
  if (nodes.length > 0) {
    keys = _.uniq(_.flatten(nodes.map(n => Object.keys(n.data || {}).filter(k => !k.startsWith('_')))));
  } else {
    try {
      const schema = gxr.getCanvasSchema();
      const nodeSchema = schema.nodes.find(n => n.label === type);
      if (nodeSchema && nodeSchema.properties) {
        keys = nodeSchema.properties.map(p => p.name).filter(k => !k.startsWith('_'));
      }
    } catch (e) {}
  }
  
  if (keys.length === 0) {
    keys = ["label", "sector", "year", "market_action", "normalized_competitor"];
  }
  
  return keys.sort();
}
```

<!--{"pinCode":false,"dname":"9b4a19a4-2c0b-4b3c-8a92-4459ae2d8c3c","codeMode":"js","hide":true}-->
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
    borderTertiary: '#2E2E2E',   // Tertiary border (neutral gray)
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

<!--{"pinCode":false,"dname":"73b7b0b8-4b87-4c78-87f4-76b0c01cf44a","codeMode":"js","hide":true}-->
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
    bgCard: '#FFFFFF',           // Card background
    bgCardLight: '#F8F9FA',      // Light card variant
    bgInput: '#FFFFFF',          // Input fields - text input area
    
    // Border colors (Subtle separation - light)
    borderPrimary: '#E1E4E8',    // Divider / border
    borderSecondary: '#E1E4E8',  // Secondary border
    borderTertiary: '#E1E4E8',   // Tertiary border
    borderFocus: 'rgba(127, 163, 200, 0.5)', // Focus ring - subtle accent (#7FA3C8 at 50%)
    
    // Text colors (Hierarchy - light mode)
    textPrimary: '#24292E',      // Body text
    textSecondary: '#1A1F24',    // Panel title
    textTertiary: '#586069',     // Section headers
    textAccent: '#24292E',       // Accent text (neutral)
    textPlaceholder: '#959DA5',  // Placeholder text
    textDisabled: '#C6CBD1',     // Disabled text
    
    // Button colors (Grayscale with brightness variation - light)
    btnPrimary: '#F6F8FA',       // Primary button default
    btnPrimaryHover: '#E1E4E8',  // Primary button hover
    btnPrimaryActive: '#D1D5DA', // Primary button active
    btnPrimaryGradient: 'linear-gradient(135deg, #F6F8FA 0%, #E1E4E8 100%)',
    btnSecondary: 'transparent', // Secondary button
    btnSecondaryHover: 'rgba(246, 248, 250, 0.5)', // Secondary hover
    
    // Toggle colors (Brightness-based, not color - light)
    toggleOff: '#D1D5DA',        // Toggle OFF state
    toggleOn: '#959DA5',         // Toggle ON state
    
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

<!--{"pinCode":false,"dname":"4d1d15b8-4a0b-4ecf-a20a-08974bb7b1d0","codeMode":"js","hide":true}-->
```js
mutable selectedTheme = 'dark'
```

<!--{"pinCode":false,"dname":"53bf0d55-75de-4edc-9d5f-2daed2f8aaf6","codeMode":"js","hide":true}-->
```js
// Active theme based on selection
theme = selectedTheme === 'dark' ? theme_dark : theme_light
```

<!--{"pinCode":false,"dname":"f106eb59-9431-4b23-80d9-b42293ac6ff7","codeMode":"js"}-->
```js
{
  const c = theme.colors;
  const s = theme.spacing;
  const t = theme.typography;
  const r = theme.borderRadius;
  const sh = theme.shadows;
  const tr = theme.transitions;
  const g = theme.gradients;

  // Internal filter state - purely local, no mutable
  let currentFilter = "";
  
  // Local selection tracking (shared via global to avoid re-render)
  const localSelection = new Set(globalThis.__companySelection || []);
  globalThis.__companySelection = localSelection;
  const setGlobalSelection = () => { globalThis.__companySelection = localSelection; };
  
  // Internal function to filter and sort (selected first)
  const getFilteredData = () => {
    const filter = currentFilter.toLowerCase();
    let data = company_data || [];
    if (filter) {
      data = data.filter(row => 
        (row.ticker && row.ticker.toLowerCase().includes(filter)) ||
        (row.name && row.name.toLowerCase().includes(filter)) ||
        (row.sic && row.sic.toString().toLowerCase().includes(filter))
      );
    }
    return [...data].sort((a, b) => {
      const aSelected = localSelection.has(a.ticker) ? 0 : 1;
      const bSelected = localSelection.has(b.ticker) ? 0 : 1;
      if (aSelected !== bSelected) return aSelected - bSelected;
      return (a.ticker || '').localeCompare(b.ticker || '');
    });
  };

  // Update selection count badge only (no table re-render)
  const updateSelectionBadge = () => {
    container.querySelector('#selection-count-badge').textContent = localSelection.size;
    const clearBtn = container.querySelector('#clear-selection-btn');
    if (clearBtn) clearBtn.style.display = localSelection.size > 0 ? 'inline-flex' : 'none';
  };

  // Internal function to render table - only called on filter change or initial load
  const renderTable = () => {
    const data = getFilteredData();
    const tableBody = container.querySelector('#table-body');
    
    if (data.length === 0) {
      tableBody.innerHTML = `<tr><td colspan="4" style="padding: ${s.sectionPadding}; text-align: center; color: ${c.textTertiary};">No companies found.</td></tr>`;
      return;
    }
    
    tableBody.innerHTML = data.map(row => `
      <tr style="border-bottom: 1px solid ${c.borderSecondary}; cursor: pointer;" class="company-row" data-ticker="${row.ticker}">
        <td style="padding: 10px; text-align: center;"><input type="checkbox" class="row-checkbox" data-ticker="${row.ticker}" ${localSelection.has(row.ticker) ? 'checked' : ''} style="cursor: pointer;" /></td>
        <td style="padding: 10px; font-weight: ${t.fontWeightBold}; color: ${c.textSecondary};">${row.ticker}</td>
        <td style="padding: 10px;">${row.name || ''}</td>
        <td style="padding: 10px; color: ${c.textTertiary};">${row.sic || ''}</td>
      </tr>
    `).join('');
    
    // Attach event listeners
    tableBody.querySelectorAll('.company-row').forEach(row => {
      row.onclick = (e) => {
        if (e.target.type === 'checkbox') return;
        const box = row.querySelector('.row-checkbox');
        box.checked = !box.checked;
        handleCheckboxChange(box);
      };
    });

    tableBody.querySelectorAll('.row-checkbox').forEach(box => {
      box.onchange = () => handleCheckboxChange(box);
    });
  };

  // Handle checkbox change - NO re-render, just update local state + badge
  const handleCheckboxChange = (box) => {
    const ticker = box.getAttribute('data-ticker');
    if (box.checked) {
      localSelection.add(ticker);
    } else {
      localSelection.delete(ticker);
    }
    setGlobalSelection();
    updateSelectionBadge();
  };

  const container = html`
<div id="company-manager-card" style="
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
  <h2 style="margin: 0 0 ${s.sectionMargin} 0; font-size: ${t.fontSizeHeading}; color: ${c.textSecondary}; display: flex; align-items: center; gap: ${s.gapSmall};">
    🏢 Company Data Manager
  </h2>
  
  <!-- Knowledge Base Section -->
  <div style="margin-bottom: ${s.sectionMargin}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: ${s.gapLarge};">
       <h3 style="margin: 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">📦 Knowledge Base</h3>
       <button id="refresh-companies-btn" style="padding: ${s.buttonPaddingSmall}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.buttonSmall}; cursor: pointer; font-weight: ${t.fontWeightBold}; font-size: ${t.fontSizeTiny}; transition: ${tr.background};"
         onmouseover="this.style.background='${c.btnPrimaryHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'">🔄 Sync DB</button>
    </div>
    
    <div style="margin-bottom: ${s.gapLarge};">
      <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; display: block; font-size: ${t.fontSizeSmall};">Search Companies</label>
      <div style="position: relative;">
        <input id="filter-input" type="text" style="background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; padding: ${s.inputPadding}; padding-right: 30px; width: 100%; box-sizing: border-box; font-size: ${t.fontSizeBody}; outline: none; transition: ${tr.border};" placeholder="Type ticker, name, or SIC..."
          onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'" />
        <button id="clear-filter-btn" title="Clear search" style="position: absolute; right: 10px; top: 50%; transform: translateY(-50%); width: 18px; height: 18px; border-radius: 9px; border: 1px solid ${c.borderTertiary}; background: ${c.btnSecondary}; color: ${c.textTertiary}; font-size: 12px; font-weight: ${t.fontWeightBold}; line-height: 1; cursor: pointer; display: none; align-items: center; justify-content: center;">×</button>
      </div>
    </div>
    
    <div>
      <div style="display: flex; align-items: center; gap: 6px; margin-bottom: ${s.gapSmall};">
        <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; display: block; font-size: ${t.fontSizeSmall};">Selection Library (<span id="selection-count-badge">${localSelection.size}</span> selected)</label>
        <button id="clear-selection-btn" title="Clear selection" style="display: ${localSelection.size > 0 ? 'inline-flex' : 'none'}; align-items: center; justify-content: center; width: 18px; height: 18px; background: ${c.btnSecondary}; border: 1px solid ${c.borderTertiary}; color: ${c.textTertiary}; cursor: pointer; font-weight: ${t.fontWeightBold}; font-size: 12px; line-height: 1; border-radius: 9px; opacity: 0.75;">×</button>
      </div>
      <div style="max-height: 350px; overflow-y: auto; border: 2px solid ${c.borderPrimary}; border-radius: ${r.section}; background: ${c.bgInput};">
        <table style="width: 100%; border-collapse: collapse; color: ${c.textPrimary}; font-size: ${t.fontSizeSmall};">
          <thead>
            <tr style="border-bottom: 2px solid ${c.borderSecondary}; position: sticky; top: 0; background: ${c.bgInput}; z-index: 1;">
              <th style="padding: 10px; text-align: center; width: 40px;"><input type="checkbox" id="select-all-box" style="cursor: pointer;" /></th>
              <th style="padding: 10px; text-align: left; color: ${c.textTertiary};">Ticker</th>
              <th style="padding: 10px; text-align: left; color: ${c.textTertiary};">Name</th>
              <th style="padding: 10px; text-align: left; color: ${c.textTertiary};">SIC</th>
            </tr>
          </thead>
          <tbody id="table-body"></tbody>
        </table>
      </div>
    </div>
  </div>
</div>`;

  // Filter input - local state only, updates table via DOM
  const filterInput = container.querySelector('#filter-input');
  const clearFilterBtn = container.querySelector('#clear-filter-btn');

  filterInput.oninput = (e) => {
    currentFilter = e.target.value;
    clearFilterBtn.style.display = currentFilter ? 'inline-flex' : 'none';
    renderTable();
  };

  clearFilterBtn.onclick = () => {
    currentFilter = "";
    filterInput.value = "";
    clearFilterBtn.style.display = 'none';
    renderTable();
    filterInput.focus();
  };

  // Select All - update local state + checkboxes directly, no table re-render
  container.querySelector('#select-all-box').onchange = (e) => {
    const checked = e.target.checked;
    const data = getFilteredData();
    
    // Update local selection
    data.forEach(row => {
      if (checked) localSelection.add(row.ticker);
      else localSelection.delete(row.ticker);
    });
    
    // Update all visible checkboxes directly (no re-render)
    container.querySelectorAll('.row-checkbox').forEach(box => {
      box.checked = checked;
    });
    
    setGlobalSelection();
    updateSelectionBadge();
  };

  container.querySelector('#clear-selection-btn').onclick = () => {
    localSelection.clear();
    setGlobalSelection();
    renderTable();
    const selectAll = container.querySelector('#select-all-box');
    if (selectAll) selectAll.checked = false;
    updateSelectionBadge();
  };

  container.querySelector('#refresh-companies-btn').onclick = () => {
    mutable refreh_flag += 1;
    gxr.toast().info("Synchronizing data...");
  };

  // Initial render
  renderTable();

  return container;
}
```

<!--{"pinCode":false,"dname":"3bb05a13-4cf9-4b5e-908b-be9afe512c9c","codeMode":"js"}-->
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
<div style="
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
  <!-- Action Intelligence Section -->
  <div style="padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
     <h3 style="margin: 0 0 ${s.gapLarge} 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">⚡ Action Intelligence</h3>
     
     <div style="margin-bottom: ${s.gapLarge};">
       <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; display: block; font-size: ${t.fontSizeSmall};">Analysis Model</label>
       <select id="analysis-select" style="background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; padding: ${s.inputPadding}; width: 100%; box-sizing: border-box; outline: none; transition: ${tr.border};"
         onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'">
         ${['Market', 'Risk', 'Competitor'].map(opt => html`<option value="${opt}" ${opt === analysis_type ? 'selected' : ''}>${opt}</option>`)}
       </select>
     </div>
     
     <button id="load-to-graph-btn" style="width: 100%; padding: ${s.buttonPadding}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.button}; font-size: ${t.fontSizeBody}; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.background}; box-shadow: ${sh.button};"
       onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">
       📥 Load Selected Data
     </button>
  </div>
</div>`;

  container.querySelector('#analysis-select').onchange = (e) => {
    mutable analysis_type = e.target.value;
  };

  container.querySelector('#load-to-graph-btn').onclick = async () => {
    const selection = globalThis.__companySelection ? Array.from(globalThis.__companySelection) : [];
    mutable selected_tickers = new Set(selection);
    
    if (selection.length === 0) {
      gxr.toast().warning("Please select companies first.");
      return;
    }
    gxr.toast().info(`Analyzing ${analysis_type}...`);
    const company_list = "['"+ selection.join("','") +"']";
    const bq = String(db || "").toLowerCase() === "bigquerygraph";
    const query = bq
      ? `MATCH (c:Company)-[r]->(m:${analysis_type}) WHERE c.id IN UNNEST(${company_list}) RETURN *`
      : `MATCH (c:Company)-[r]->(m:${analysis_type}) WHERE c.id IN ${company_list} RETURN *`;
    try {
      await gxr.query(query);
      propagateCompanyName();
      gxr.toast().success("Analysis loaded.");
    } catch (e) { gxr.toast().error(e.message); }
  };

  return container;
}
```

<!--{"pinCode":false,"dname":"e58f5f68-5e0e-4728-aa98-5c8735dd1fe7","codeMode":"js", "hide": true}-->
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
<div style="
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
  <!-- Extraction Section -->
  <div style="padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
     <h3 style="margin: 0 0 ${s.gapLarge} 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">✨ Graph Distillation</h3>
     
     <div style="margin-bottom: ${s.gapLarge};">
       <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; display: block; font-size: ${t.fontSizeSmall};">Target Property from <span id="analysis-label-sync">${analysis_type}</span></label>
       <div style="display: flex; gap: ${s.gapSmall};">
         <select id="prop-select" style="flex: 1; background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; padding: ${s.inputPadding}; outline: none; transition: ${tr.border};"
           onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'">
           <option value="">-- Choose property --</option>
           ${prop_list.map(p => html`<option value="${p}" ${p === selected_extract_prop ? 'selected' : ''}>${p}</option>`)}
         </select>
         <button id="refresh-props-btn" style="padding: 0 ${s.gapSmall}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: 1px solid ${c.borderPrimary}; border-radius: ${r.buttonSmall}; cursor: pointer; transition: ${tr.background};"
           onmouseover="this.style.background='${c.btnPrimaryHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'">🔄</button>
       </div>
     </div>
     
     <button id="run-extraction-btn" style="width: 100%; padding: ${s.inputPadding}; background: ${c.btnSecondary}; color: ${c.textPrimary}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.button}; font-weight: ${t.fontWeightBold}; cursor: pointer; display: ${selected_extract_prop ? 'block' : 'none'}; transition: ${tr.background};"
       onmouseover="this.style.background='${c.btnSecondaryHover}'" onmouseout="this.style.background='${c.btnSecondary}'">
       Distill ${selected_extract_prop} ➔ ${toPascalCase(selected_extract_prop)}
     </button>
     
     <div id="prop-fallback" style="color: ${c.textTertiary}; font-size: ${t.fontSizeTiny}; text-align: center; font-style: italic; display: ${selected_extract_prop ? 'none' : 'block'};">Select a target property to enable entity distillation.</div>
  </div>
</div>`;

  container.querySelector('#prop-select').onchange = (e) => {
    mutable selected_extract_prop = e.target.value;
    const btn = container.querySelector('#run-extraction-btn');
    const fallback = container.querySelector('#prop-fallback');
    if (e.target.value) {
      btn.style.display = 'block';
      btn.textContent = `Distill ${e.target.value} ➔ ${toPascalCase(e.target.value)}`;
      fallback.style.display = 'none';
    } else {
      btn.style.display = 'none';
      fallback.style.display = 'block';
    }
  };

  container.querySelector('#refresh-props-btn').onclick = () => {
    gxr.toast().info("Updating property list...");
    const current = analysis_type;
    mutable analysis_type = "";
    mutable analysis_type = current;
  };

  container.querySelector('#run-extraction-btn').onclick = async () => {
    const prop = selected_extract_prop;
    const targetCategory = toPascalCase(prop);
    const relationship = prop.toUpperCase();
    gxr.toast().info(`Distilling ${prop}...`);
    try {
      gxr.extract({ sourceCategory: analysis_type, targetCategory, props: [{ name: prop, isKey: true, isSplit: true, splitChar: "," }], relationship, inheritLinks: true });
      await gxr.sleep(300);
      gxr.dispatchGraphDataUpdate();
      await gxr.sleep(300);
      gxr.edges(`:${relationship}`).filter(e=>e.source.category==targetCategory).remove();
      await gxr.sleep(300);
      gxr.forceLayout();
      gxr.setCategoryColor(targetCategory, "#00d4ff");
      gxr.toast().success("Distillation finished.");
    } catch (e) { gxr.toast().error(e.message); }
  };

  return container;
}
```

<!--{"pinCode":false,"dname":"191b8f1e-7f05-4c1c-92fc-0b1fec5d580f","codeMode":"js","hide":true}-->
```js
md `#### Appendix`
```

<!--{"pinCode":false,"dname":"e181c8b6-1d75-4623-b30d-0d4e21a26dac","codeMode":"js","hide":true}-->
```js
function toPascalCase(str) {
  if (!str) return "";
  return str
    .split('_')
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join('');
}
```

<!--{"pinCode":false,"dname":"bdfe8735-d8b3-4f68-b07f-fb82b99dd6d3","codeMode":"js","hide":true}-->
```js
BQGQueryToTable = async (query) =>{
  let res = await gxr.query(query, {saveToGraph : false})
  return parseQueryTable(res.data)
}
```

<!--{"pinCode":false,"dname":"2ec6c2fe-6e71-42a2-ba6f-935c2ca50d65","codeMode":"js","hide":true}-->
```js
parseQueryTable = (data)=>{  if(data.length==0) return []
  else{
    const [header, ...rows]=data;
    const jsonArray = rows.map(row => {
      return Object.fromEntries(header.map((key, index) => [key, row[index]]));
});
    return jsonArray
  }
}
```

<!--{"pinCode":false,"dname":"0b86cb67-d8f7-4607-a9d2-4b9d6fc0fd67","codeMode":"js","hide":true}-->
```js
propagateCompanyName = ()=>
{
  gxr.nodes(':Company').forEach(c=>{
    c.properties.company = c.properties.id
  })
  let myedges = gxr.edges().array.filter(e=>['Market', 'Risk', 'Competitor'].includes(e.target.category))
  myedges.forEach(e=>{
    e.target.properties.company = e.source.properties.id
  })
}
```
