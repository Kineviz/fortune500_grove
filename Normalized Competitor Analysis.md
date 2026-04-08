<!--{"pinCode":false,"dname":"2776643e-50df-4227-8432-9a2a78f05fe0","codeMode":"markdown"}-->
```md
### Competitor Cohort Analysis
```

<!--{"pinCode":false,"dname":"9b4a19a4-2c0b-4b3c-8a92-4459ae2d8c3c","codeMode":"js","hide":true}-->
```js
// ============================================================================
// DARK THEME CONFIGURATION - Neutral Grayscale
// ============================================================================

theme_dark = ({
  colors: {
    bgPrimary: '#1C1C1C',
    bgSecondary: '#242424',
    bgCard: '#242424',
    bgCardLight: '#272727',
    bgInput: '#1F1F1F',
    borderPrimary: '#2E2E2E',
    borderSecondary: '#2E2E2E',
    borderTertiary: '#2E2E2E',
    borderFocus: 'rgba(127, 163, 200, 0.4)',
    textPrimary: '#B8BDC5',
    textSecondary: '#D8DCE2',
    textTertiary: '#9CA3AD',
    textAccent: '#B8BDC5',
    textPlaceholder: '#6F7680',
    textDisabled: '#4A4F57',
    btnPrimary: '#2E2E2E',
    btnPrimaryHover: '#373737',
    btnPrimaryActive: '#404040',
    btnPrimaryGradient: 'linear-gradient(135deg, #2E2E2E 0%, #373737 100%)',
    btnSecondary: 'transparent',
    btnSecondaryHover: 'rgba(46, 46, 46, 0.2)',
    toggleOff: '#404040',
    toggleOn: '#7A7A7A',
    shadowPrimary: 'rgba(0,0,0,0.4)',
    shadowButton: 'rgba(0,0,0,0.2)',
    shadowButtonHover: 'rgba(0,0,0,0.3)',
  },
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
  borderRadius: {
    card: '6px',
    section: '4px',
    input: '4px',
    button: '4px',
    buttonSmall: '4px',
  },
  shadows: {
    card: '0 2px 12px rgba(0,0,0,0.2)',
    button: '0 2px 8px rgba(0,0,0,0.15)',
    buttonHover: '0 4px 12px rgba(0,0,0,0.2)',
  },
  transitions: {
    default: '0.2s',
    transform: 'transform 0.2s, box-shadow 0.2s',
    background: 'background 0.2s',
    border: 'border-color 0.2s',
    button: 'background 0.2s, color 0.2s',
  },
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
// ============================================================================

theme_light = ({
  colors: {
    bgPrimary: '#F8F9FA',
    bgSecondary: '#FFFFFF',
    bgCard: '#FFFFFF',
    bgCardLight: '#F8F9FA',
    bgInput: '#FFFFFF',
    borderPrimary: '#E1E4E8',
    borderSecondary: '#E1E4E8',
    borderTertiary: '#E1E4E8',
    borderFocus: 'rgba(127, 163, 200, 0.5)',
    textPrimary: '#24292E',
    textSecondary: '#1A1F24',
    textTertiary: '#586069',
    textAccent: '#24292E',
    textPlaceholder: '#959DA5',
    textDisabled: '#C6CBD1',
    btnPrimary: '#F6F8FA',
    btnPrimaryHover: '#E1E4E8',
    btnPrimaryActive: '#D1D5DA',
    btnPrimaryGradient: 'linear-gradient(135deg, #F6F8FA 0%, #E1E4E8 100%)',
    btnSecondary: 'transparent',
    btnSecondaryHover: 'rgba(246, 248, 250, 0.5)',
    toggleOff: '#D1D5DA',
    toggleOn: '#959DA5',
    shadowPrimary: 'rgba(0,0,0,0.1)',
    shadowButton: 'rgba(0,0,0,0.08)',
    shadowButtonHover: 'rgba(0,0,0,0.12)',
  },
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
  borderRadius: {
    card: '6px',
    section: '4px',
    input: '4px',
    button: '4px',
    buttonSmall: '4px',
  },
  shadows: {
    card: '0 4px 20px rgba(0,0,0,0.1)',
    button: '0 4px 15px rgba(0,0,0,0.08)',
    buttonHover: '0 6px 20px rgba(0,0,0,0.12)',
  },
  transitions: {
    default: '0.2s',
    transform: 'transform 0.2s, box-shadow 0.2s',
    background: 'background 0.2s',
    border: 'border-color 0.2s',
    button: 'background 0.2s, color 0.2s',
  },
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
theme = selectedTheme === 'dark' ? theme_dark : theme_light
```

<!--{"pinCode":false,"dname":"edc52032-d57d-495e-8e55-1ad6f83aed11","codeMode":"js","hide":true}-->
```js
mutable selectionRefreshFlag = 0
```

<!--{"pinCode":false,"dname":"4a3bbe9f-dc4b-4406-bdc6-c27b170ae7cc","codeMode":"js","hide":true}-->
```js
selectedNodes = {
  selectionRefreshFlag // dependency for reactivity
  try {
    const selected = gxr.nodes("[[selected]]").toArray();
    return selected;
  } catch (e) {
    return [];
  }
}
```

<!--{"pinCode":false,"dname":"60c11b2b-6662-4b31-b492-875e4451ae28","codeMode":"js","hide":true}-->
```js
selectedNodesData =  selectedNodes.map(node => ({
      id: node.id,
      category: node.category || 'Unknown',
      label: node.properties?.label || node.properties?.id || node.id
    }))
```

<!--{"pinCode":false,"dname":"679c8fd3-bc1e-4b4c-9cfc-dd234768e804","codeMode":"js","hide":true}-->
```js
// Set up GraphXR selection change listener
gxr.onSelect('select', (e) => {
  mutable selectionRefreshFlag += 1;
});
```

<!--{"pinCode":false,"dname":"a25bca03-3023-4e9f-9cc5-c1aa0917d187","codeMode":"js","hide":true}-->
```js
selectedNormalizedCompetitors = selectedNodes.filter(n=>n.category=='NormalizedCompetitor')
```

<!--{"pinCode":false,"dname":"1d947761-62dd-4aa9-a627-1ccae25338fc","codeMode":"js","hide":true}-->
```js
normalized_to_edges = gxr.edges({relationship:'INSTANCE_OF'}).toArray()
```

<!--{"pinCode":false,"dname":"03eb07f1-bab9-4c6f-b9aa-f0ebb7486ba7","codeMode":"js","hide":true}-->
```js
target_competitor_category_node = {
  if (selectedNormalizedCompetitors.length>0){
    let target_node = selectedNormalizedCompetitors[0]
    let linked_competitor_nodes = _.chain(normalized_to_edges)
      .filter(e=>e.targetId==target_node.id)
      .map(e=>e.source)
      .value()
    return {
      normalizedRisk: target_node,
      linked_competitors: linked_competitor_nodes
    }
  }else{
    return {}
  }
}
```

<!--{"pinCode":false,"dname":"3e8ef506-7583-4813-b653-50870ebe2d32","codeMode":"js"}-->
```js
{
  const c = theme.colors;
  const s = theme.spacing;
  const t = theme.typography;
  const r = theme.borderRadius;
  const sh = theme.shadows;
  const tr = theme.transitions;
  const g = theme.gradients;

  const container = document.createElement("div");
  container.style.margin = s.cardMargin;
  container.style.padding = s.cardPadding;
  container.style.background = g.cardBackground;
  container.style.borderRadius = r.card;
  container.style.border = `2px solid ${c.borderPrimary}`;
  container.style.boxShadow = sh.card;
  container.style.fontFamily = t.fontFamily;
  container.style.width = "100%";
  container.style.boxSizing = "border-box";
  container.style.color = c.textPrimary;

  let content = "";

  if (target_competitor_category_node.normalizedRisk) {
    let competitor = target_competitor_category_node.normalizedRisk.properties.normalized_competitor;
    let occurrence = target_competitor_category_node.normalizedRisk.properties.occurrence;
    let associated_companies = target_competitor_category_node.normalizedRisk.properties.associated_companies;
    let companies = _.uniq(target_competitor_category_node.linked_competitors.map(n => n.properties.company_name));

    let plotHtml = "";
    if (by_year.length > 1) {
      const plot = Plot.plot({
        height: 250,
        margin: 40,
        style: {
          fontSize: 14,
          "--plot-title-font-size": "10px",
          color: c.textPrimary,
          background: "transparent"
        },
        marks: [
          Plot.ruleY([0], {stroke: c.borderSecondary}),
          Plot.line(by_year, {x: "year", y: "count", stroke: "#7FA3C8"}),
          Plot.dot(by_year, {x: "year", y: "count", fill: "#7FA3C8", r: 4, tip: true})
        ],
        x: {
          label: "Year",
          tickFormat: "d"
        },
        y: {
          label: "Count",
          grid: true
        }
      });
      plotHtml = plot.outerHTML;
    } else {
      plotHtml = `<div style="color: ${c.textTertiary}; font-style: italic; text-align: center; padding: ${s.sectionPadding};">Select a NormalizedRisk node to see the trend</div>`;
    }

    content = `
      <div style="margin-bottom: ${s.sectionMargin}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
        <h3 style="margin: 0 0 ${s.gapLarge} 0; color: ${c.textSecondary}; font-size: ${t.fontSizeSubheading};">${competitor}</h3>
        <div style="display: flex; gap: ${s.gapLarge}; margin-bottom: ${s.gapLarge};">
          <div style="flex: 1; background: ${c.bgInput}; padding: ${s.inputPadding}; border-radius: ${r.input}; border: 1px solid ${c.borderPrimary}; text-align: center;">
            <div style="font-size: ${t.fontSizeHeading}; font-weight: ${t.fontWeightBold}; color: ${c.textPrimary};">${occurrence || 0}</div>
            <div style="font-size: ${t.fontSizeSmall}; color: ${c.textTertiary};">Occurrences</div>
          </div>
          <div style="flex: 1; background: ${c.bgInput}; padding: ${s.inputPadding}; border-radius: ${r.input}; border: 1px solid ${c.borderPrimary}; text-align: center;">
            <div style="font-size: ${t.fontSizeHeading}; font-weight: ${t.fontWeightBold}; color: ${c.textPrimary};">${associated_companies || 0}</div>
            <div style="font-size: ${t.fontSizeSmall}; color: ${c.textTertiary};">Associated Companies</div>
          </div>
        </div>
        <div style="margin-bottom: ${s.gapLarge};">
          ${plotHtml}
        </div>
        <div>
          <h4 style="margin: 0 0 ${s.gapSmall} 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSmall};">Associated Companies</h4>
          <ul style="margin: 0; padding-left: 20px; color: ${c.textPrimary}; font-size: ${t.fontSizeSmall};">
            ${companies.map(comp => `<li style="margin-bottom: 4px;">${comp}</li>`).join('')}
          </ul>
        </div>
      </div>
    `;
  } else {
    content = `
      <div style="padding: ${s.sectionPadding}; text-align: center; color: ${c.textTertiary}; font-style: italic;">
        Select a NormalizedCompetitor node to view analysis.
      </div>
    `;
  }

  container.innerHTML = `
    <h2 style="margin: 0 0 ${s.sectionMargin} 0; font-size: ${t.fontSizeHeading}; color: ${c.textSecondary}; display: flex; align-items: center; gap: ${s.gapSmall};">
      🎯 Competitor Cohort Analysis
    </h2>
    ${content}
  `;

  return container;
}
```

<!--{"pinCode":false,"dname":"1f40b519-17d5-4008-84ea-e41903983492","codeMode":"js","hide":true}-->
```js
// Plot logic moved to UI
```

<!--{"pinCode":false,"dname":"3ea81eaf-578e-4c80-8720-efebb6298621","codeMode":"js","hide":true}-->
```js
// Stats logic moved to UI
```

<!--{"pinCode":false,"dname":"eef8c9b1-ccce-4768-a801-42a46493c186","codeMode":"js","hide":true}-->
```js
by_year = {
  if (target_competitor_category_node.linked_competitors && target_competitor_category_node.linked_competitors.length > 0) {
    const yearCounts = _.countBy(target_competitor_category_node.linked_competitors, n => n.properties.year);
    return Object.entries(yearCounts).map(([year, count]) => ({
      year: parseInt(year) || year,
      count: count
    })).sort((a, b) => a.year - b.year);
  }
  return [];
}
```
