<!--{"pinCode":false,"dname":"41f59035-1256-4003-9c43-317f9fb617a3","codeMode":"markdown"}-->
```md
#### Risk analysis
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

<!--{"pinCode":false,"dname":"a622f2c1-6dad-4d90-bf13-b7883f69b48c","codeMode":"js","hide":true}-->
```js
mutable refresh_flag = 0
```

<!--{"pinCode":false,"dname":"3b9608e1-d20a-4e3f-84a0-ef2a48d39b76","codeMode":"js","hide":true}-->
```js
// Refresh button logic moved to UI
```

<!--{"pinCode":false,"dname":"e9be6e82-9c1e-4702-8149-f03bdd3ff56a","codeMode":"js","hide":true}-->
```js
// Extract button logic moved to UI
```

<!--{"pinCode":false,"dname":"fe5bf490-3ca4-4ce7-b8ba-64b4e7b8317d","codeMode":"js","hide":true}-->
```js
// Timeline button logic moved to UI
```

<!--{"pinCode":false,"dname":"b879de25-2edc-40e1-9010-adf95e0057a0","codeMode":"js","hide":true}-->
```js
risk_categories = {
  refresh_flag
  let risk_categories = _.chain(gxr.nodes(':Risk').values())
    .map(n=>n.properties.risk_categories)
    .filter(n=>n!==undefined)
    .map(n=>n.split(','))
    .flatten()
    .map(n=>n.trim())
    .uniq()
    .value()
  
  let per_item_list = gxr.nodes(':Risk')
    .filter(n=>n.properties.risk_categories!==undefined)
    .map(n=>
         n.properties.risk_categories.split(',')
           .map(d=>d.trim())
      )
//   	return per_item_list
  
  return risk_categories
    .map(r=>{
      let cnt = per_item_list
         .filter(l=>l.includes(r))
         .length
      return {category: r, cnt}
    })
  
}
```

<!--{"pinCode":false,"dname":"dd15d96a-ea95-4ce6-affd-7daf4e46707c","codeMode":"js"}-->
```js
viewof sel_categories = {
  refresh_flag;
  const c = theme.colors;
  const s = theme.spacing;
  const t = theme.typography;
  const r = theme.borderRadius;
  const sh = theme.shadows;
  const tr = theme.transitions;
  const g = theme.gradients;

  const data = risk_categories.map(category => ({category: category.category, cnt: category.cnt}));
  
  // Local selection tracking
  const localSelection = new Set(data.map(d => d.category));
  
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
  <h2 style="margin: 0 0 ${s.sectionMargin} 0; font-size: ${t.fontSizeHeading}; color: ${c.textSecondary}; display: flex; align-items: center; gap: ${s.gapSmall};">
    ⚠️ Risk Analysis
  </h2>
  
  <!-- Action Section -->
  <div style="margin-bottom: ${s.sectionMargin}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
    <div style="display: flex; gap: ${s.gapMedium}; flex-wrap: wrap;">
      <button id="refresh-btn" style="flex: 1; padding: ${s.buttonPadding}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.button}; font-size: ${t.fontSizeBody}; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.background}; box-shadow: ${sh.button};"
        onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">
        🔄 Refresh Data
      </button>
      <button id="extract-btn" style="flex: 1; padding: ${s.buttonPadding}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.button}; font-size: ${t.fontSizeBody}; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.background}; box-shadow: ${sh.button};"
        onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">
        ✨ Extract RiskCategory by Year
      </button>
      <button id="timeline-btn" style="flex: 1; padding: ${s.buttonPadding}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.button}; font-size: ${t.fontSizeBody}; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.background}; box-shadow: ${sh.button};"
        onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">
        📈 Timeline by Category
      </button>
    </div>
  </div>

  <!-- Categories Section -->
  <div style="padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: ${s.gapLarge};">
       <h3 style="margin: 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">📋 Risk Categories</h3>
       <span style="color: ${c.textTertiary}; font-size: ${t.fontSizeSmall};"><span id="selection-count">${localSelection.size}</span> selected</span>
    </div>
    
    <div style="max-height: 350px; overflow-y: auto; border: 2px solid ${c.borderPrimary}; border-radius: ${r.section}; background: ${c.bgInput};">
      <table style="width: 100%; border-collapse: collapse; color: ${c.textPrimary}; font-size: ${t.fontSizeSmall};">
        <thead>
          <tr style="border-bottom: 2px solid ${c.borderSecondary}; position: sticky; top: 0; background: ${c.bgInput}; z-index: 1;">
            <th style="padding: 10px; text-align: center; width: 40px;"><input type="checkbox" id="select-all-box" checked style="cursor: pointer;" /></th>
            <th style="padding: 10px; text-align: left; color: ${c.textTertiary};">Category</th>
            <th style="padding: 10px; text-align: left; color: ${c.textTertiary};">Count</th>
          </tr>
        </thead>
        <tbody id="table-body">
          ${data.map(row => `
            <tr style="border-bottom: 1px solid ${c.borderSecondary}; cursor: pointer;" class="category-row" data-category="${row.category}">
              <td style="padding: 10px; text-align: center;"><input type="checkbox" class="row-checkbox" data-category="${row.category}" checked style="cursor: pointer;" /></td>
              <td style="padding: 10px; font-weight: ${t.fontWeightBold}; color: ${c.textSecondary};">${row.category}</td>
              <td style="padding: 10px; color: ${c.textTertiary};">${row.cnt}</td>
            </tr>
          `).join('')}
        </tbody>
      </table>
    </div>
  </div>
</div>`;

  // Define value property for Observable viewof
  Object.defineProperty(container, "value", {
    get() {
      return data.filter(d => localSelection.has(d.category));
    }
  });

  const updateSelection = () => {
    container.querySelector('#selection-count').textContent = localSelection.size;
    container.dispatchEvent(new CustomEvent("input"));
  };

  container.querySelectorAll('.category-row').forEach(row => {
    row.onclick = (e) => {
      if (e.target.type === 'checkbox') return;
      const box = row.querySelector('.row-checkbox');
      box.checked = !box.checked;
      const category = box.getAttribute('data-category');
      if (box.checked) localSelection.add(category);
      else localSelection.delete(category);
      updateSelection();
    };
  });

  container.querySelectorAll('.row-checkbox').forEach(box => {
    box.onchange = (e) => {
      const category = box.getAttribute('data-category');
      if (box.checked) localSelection.add(category);
      else localSelection.delete(category);
      updateSelection();
    };
  });

  container.querySelector('#select-all-box').onchange = (e) => {
    const checked = e.target.checked;
    container.querySelectorAll('.row-checkbox').forEach(box => {
      box.checked = checked;
      const category = box.getAttribute('data-category');
      if (checked) localSelection.add(category);
      else localSelection.delete(category);
    });
    updateSelection();
  };

  // Button actions
  container.querySelector('#refresh-btn').onclick = () => {
    mutable refresh_flag += 1;
    gxr.toast().info("Refreshing data...");
  };

  container.querySelector('#extract-btn').onclick = async () => {
    gxr.toast().info("Extracting RiskCategory...");
    let relationship = 'IN_RISK_CATEGORY';
    let targetCategory = 'RiskCategory';
    try {
      gxr.extract({
        sourceCategory: 'Risk',
        targetCategory,
        props: [
          { name: 'risk_categories', isKey: true, isSplit: true, splitChar: "," },
          { name: 'year', isKey: true }
        ],
        relationship,
        inheritLinks: true
      });
      await gxr.sleep(200);
      gxr.dispatchGraphDataUpdate();
      
      await gxr.sleep(200);
      gxr.edges(`:${relationship}`).filter(e=>e.source.category==targetCategory).remove();
      
      await gxr.nodes({category:'Company'}).remove();

      await gxr.sleep(200);
      gxr.forceLayout();
      gxr.setCategoryColor(targetCategory, "#00ffaa");
      gxr.toast().success("Extraction finished.");
    } catch (e) {
      gxr.toast().error(e.message);
    }
  };

  container.querySelector('#timeline-btn').onclick = async () => {
    gxr.toast().info("Generating timeline...");
    try {
      await gxr.setGraphMode('2D');
      await plotTimeline('year', 'risk_categories');
      await gxr.sleep(300);
      
      await gxr.nodes(":RiskCategory").ego({
        depth: 1,
        edgeLength: 0.2,
        mode: 'tree',
        orientation: 'right',
      });
      
      await gxr.nodes(":RiskCategory").shift({ z: 0.4 });
      await gxr.sleep(300);

      await flyToViewPoint();
      gxr.toast().success("Timeline generated.");
    } catch (e) {
      gxr.toast().error(e.message);
    }
  };

  // Dispatch initial value
  container.dispatchEvent(new CustomEvent("input"));

  return container;
}
```

<!--{"pinCode":false,"dname":"3c1fdb9d-4958-4092-a637-a20b5f3b0dab","codeMode":"js","hide":true}-->
```js
// Hide Risk and RiskCategory by selection on the table above
{
  let sel_category_list = sel_categories.map(n=>n.category)
  gxr.nodes(':RiskCategory')
    .filter(n=>sel_category_list.includes(n.properties.risk_categories))
    .show()
  
  await gxr.sleep(200)
  
  gxr.nodes(':Company')
    .hide()
  
  gxr.nodes(':RiskCategory')
    .filter(n=>!sel_category_list.includes(n.properties.risk_categories))
    .hide()
  
  return gxr.nodes(':Risk')
    .filter(n=>{
      let cats = (n.properties.risk_categories ? n.properties.risk_categories.split(',') : [])
        .map(d=>d.trim())
    
      return _.intersection(sel_category_list, cats).length == 0
    })
    .hide()
}
```

<!--{"pinCode":false,"dname":"c3f1bc58-d365-46f8-94a2-3078017ef23a","codeMode":"js"}-->
```js
md `---`
```

<!--{"pinCode":false,"dname":"4c963a37-7813-4e43-8621-c4a1039ea156","codeMode":"js","hide":true}-->
```js
md `#### Appendix`
```

<!--{"pinCode":false,"dname":"f49a9735-77fd-4ff7-9102-a941d864f5cf","codeMode":"js","hide":true}-->
```js
// TWEEN = require('es6-tween')
// TWEEN.autoPlay(true)
flyToViewPoint =  async ()=>{
  await gxr.setGraphMode('3D')
  let scene = _app.controller.drawing.cloudScene
  
  let z0= scene.position.z
  let z1 = -0.7
  let r0 = 0
  let r1 = -0.8
  let x={t:0}
  new TWEEN.Tween(x)
    .to({t:1}, 1000)
    .on('update', ()=>{
      scene.position.setZ(z0*(1-x.t)+ z1*x.t)
      scene.setRotationFromAxisAngle(new TRHEE.Vector3(1, 0), r0*(1-x.t)+r1*x.t)
    })
//     .on('complete', ()=>{
//       scene.setRotationFromAxisAngle(new TRHEE.Vector3(1, 0, 0), -1.0)
//     })
    .start()
}
```

<!--{"pinCode":false,"dname":"af8242b3-5e12-4b0a-b5a2-88e6ea8740b5","codeMode":"js","hide":true}-->
```js
import {plotTimeline} from 'Timeline.md'
```

<!--{"pinCode":false,"dname":"30b04174-ac80-4b4e-b596-3a9a4ad3ba26","codeMode":"js","hide":true}-->
```js
openaiLib = import('https://cdn.skypack.dev/openai')
```

<!--{"pinCode":false,"dname":"198a0b9c-bf83-4271-be63-3b9e92bcd58a","codeMode":"js","hide":true}-->
```js
TRHEE = _app.controller.drawing.THREE
```

<!--{"pinCode":false,"dname":"54f03353-90ae-4cce-b7e1-bf9d99280810","codeMode":"js","hide":true}-->
```js
TWEEN.autoPlay(true)
```

<!--{"pinCode":false,"dname":"a9e85e4d-ccd3-49da-aa7b-1ff57478e8bb","codeMode":"js","hide":true}-->
```js
TWEEN = require('es6-tween')
```
