``` plotChart
{
 "dname": "a988738d-3b5a-4eba-965c-03e224e2ca37",
 "customise": {
  "title": "",
  "subtitle": "",
  "caption": "",
  "height": "",
  "widthType": "auto",
  "width": 300,
  "marginTop": "",
  "marginRight": "",
  "marginBottom": "",
  "marginLeft": "",
  "insetTop": "",
  "insetRight": "",
  "insetBottom": "",
  "insetLeft": "",
  "xLabel": "",
  "xScale": "",
  "xOptions": [],
  "yLabel": "",
  "yScale": "",
  "yOptions": [],
  "colorLegend": true,
  "colorScheme": "",
  "colorScale": "",
  "colorOptions": []
 },
 "marks": {
  "xf": "",
  "x": "",
  "yf": "",
  "y": "",
  "colorf": "",
  "color": "",
  "sizef": "",
  "size": "",
  "fx": "",
  "fy": "",
  "mark": ""
 }
}
```

<!--{"pinCode":false,"dname":"41f59035-1256-4003-9c43-317f9fb617a3","codeMode":"markdown"}-->
#### Risk analysis

<!--{"pinCode":false,"dname":"a622f2c1-6dad-4d90-bf13-b7883f69b48c","codeMode":"js","hide":true}-->
```js
mutable refresh_flag = 0
```

<!--{"pinCode":false,"dname":"3b9608e1-d20a-4e3f-84a0-ef2a48d39b76","codeMode":"js"}-->
```js
Inputs.button('Refresh', {reduce: ()=>{
//     gxr.nodes()
//     .show()
  mutable refresh_flag+=1
}})
```

<!--{"pinCode":false,"dname":"e9be6e82-9c1e-4702-8149-f03bdd3ff56a","codeMode":"js"}-->
```js
Inputs.button('Extract RiskCategory by Year', {reduce: async ()=>{
  let relationship = 'IN_RISK_CATEGORY'
  let targetCategory = 'RiskCategory'
  gxr.extract({
    sourceCategory: 'Risk',
    targetCategory,
    props: [
      {
        name: 'risk_categories',
        isKey: true,
        isSplit: true,
        splitChar: ","
      },
      {
        name: 'year',
        isKey: true
      }
    ],
    relationship,
    inheritLinks: true
  })
  await gxr.sleep(200)
  gxr.dispatchGraphDataUpdate();
  
  await gxr.sleep(200)
//   clea up, current extract has a bug when both split and inheritLinks are selected
 gxr.edges(`:${relationship}`).filter(e=>e.source.category==targetCategory).remove()
  
  // let's also remove Company node, we can always expand to bring it back
  await gxr.nodes({category:'Company'}).remove()

  await gxr.sleep(200)
  gxr.forceLayout();
  gxr.setCategoryColor(targetCategory, "#00ffaa");
  
  
}})
```

<!--{"pinCode":false,"dname":"fe5bf490-3ca4-4ce7-b8ba-64b4e7b8317d","codeMode":"js"}-->
```js
Inputs.button('Timeline by Category', {reduce: async ()=>
{
  await gxr.setGraphMode('2D')
  
  await plotTimeline('year', 'risk_categories')
  await gxr.sleep(300)
  
  await gxr.nodes(":RiskCategory").ego({
    depth: 1,
    edgeLength: 0.2,
    mode: 'tree', // 'tree' or 'rings'; default 'tree'
    orientation: 'right', // 'up', 'down', 'left', or 'right'; default 'right'
  })
  
  await gxr.nodes(":RiskCategory").shift({
    z: 0.4
  })
  await gxr.sleep(300)

  await flyToViewPoint()
}})
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
  refresh_flag
  let data = risk_categories.map(category=>({category:category}))
  return Inputs.table(risk_categories,
     {value: risk_categories, multiple:true, required:false})
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

<!--{"pinCode":false,"dname":"e3586bc1-dd32-48d4-b06b-f742c83a21ca","codeMode":"js"}-->
```js
viewof custom_prompt = Inputs.textarea({label: 'Your quesiton here. Leave it blank for default analysis', value:'', rows:4})
```

<!--{"pinCode":false,"dname":"3a1d3561-22b5-495f-9c22-f1fc8b751bc7","codeMode":"js","hide":true}-->
```js
mutable summaryText=''
```

<!--{"pinCode":false,"dname":"cf7153ca-b5d2-499e-8336-e9817604f936","codeMode":"js"}-->
```js
// viewof results = Inputs.textarea({value:summaryText, rows:20})
md `${summaryText}`
```

<!--{"pinCode":false,"dname":"3664a4cd-f416-488c-991a-9e78e87b4820","codeMode":"js"}-->
```js
Button(`AI Analysis`, async () => {
  let prompt = custom_prompt.length > 3 ? custom_prompt : default_prompt
  let items = gxr.nodes(':Risk')
  .map(n=>_.pick(n.properties, ['company', 'label', 'year', 'description']))
                                
  const response = await client.responses.create({
    model: config.model,
    instructions: `
You analyze a list of SEC 10-K extracted items: [{company_name, filing_year, risk_label, risk_description}, ...].

Return ONLY this short text format (no extra sections):

${prompt}

Rules:
- Be selective: include only the strongest signals from the descriptions.
- No re-classification, no canonicalization, no taxonomy.
- No invented facts; if unclear, skip it rather than guessing.
- Keep the whole output under ~150 words.
`,
    input: JSON.stringify({ items }),
    // No text.format => default free-form text
  });

  mutable summaryText = response.output_text;
});
```

<!--{"pinCode":false,"dname":"00506a64-b8ff-4e40-b52c-71371f305eab","codeMode":"js","hide":true}-->
```js
default_prompt = `
MOST SIGNIFICANT ITEMS
- up to 5 bullets total. Each bullet: (YEAR) label — 1 short clause.

MAJOR CHANGES OVER TIME
- up to 5 bullets total. Each bullet: YEAR→YEAR change in 1 short clause.

WHAT STANDS OUT
- up to 5 bullets total. Each bullet: 1 short clause.
`
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
import {plotTimeline} from 'Timeline_V1'
```

<!--{"pinCode":false,"dname":"5489fe4e-9830-4fa2-acc7-7ffb7e17067b","codeMode":"js","hide":true}-->
```js
client = new openaiLib.OpenAI({apiKey: config.apiKey, dangerouslyAllowBrowser: true});
```

<!--{"pinCode":false,"dname":"0e2455db-7663-4685-9019-8c54823d80c0","codeMode":"js","hide":true}-->
```js
config = ({
  apiKey,
  model: "gpt-5-mini",
})
```

<!--{"pinCode":false,"dname":"e897fe4c-ba5d-4c62-8b46-4fe8a9f5d4f3","codeMode":"js","hide":true}-->
```js
apiKey = getOpenAIKey()
```

<!--{"pinCode":false,"dname":"30b04174-ac80-4b4e-b596-3a9a4ad3ba26","codeMode":"js","hide":true}-->
```js
openaiLib = import('https://cdn.skypack.dev/openai')
```

<!--{"pinCode":false,"dname":"9d22aaf6-b5d6-4682-b08a-e4c1e041176c","codeMode":"js","hide":true}-->
```js
import { getOpenAIKey } from "llm"
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
