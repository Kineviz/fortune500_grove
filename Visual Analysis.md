<!--{"pinCode":false,"dname":"9ce6aae2-489e-4180-af33-10bd53dc62a0","codeMode":"js","hide":true}-->
```js
mutable GOOGLE_API_KEY = Secret("GOOGLE_API_KEY");
```

<!--{"pinCode":false,"dname":"13559424-f47d-438d-9273-a30ef0b4406a","codeMode":"js","hide":true}-->
```js
mutable selectedModel = "models/gemini-3-flash-preview";
```

<!--{"pinCode":false,"dname":"4f498595-fe28-4acc-a2d5-5dc5a08eda20","codeMode":"js","hide":true}-->
```js
mutable availableModels = [];
```

<!--{"pinCode":false,"dname":"c3983e23-298b-47fd-af56-797060984712","codeMode":"js","hide":true}-->
```js
mutable modelsLoading = false;
```

<!--{"pinCode":false,"dname":"a8ad5fbf-a15c-4ec7-a5c7-9ab122fe0d02","codeMode":"js","hide":true}-->
```js
backtick = String.fromCharCode(96);
```

<!--{"pinCode":false,"dname":"36328439-e3c0-417a-af30-eee2a9d74d63","codeMode":"js","hide":true}-->
```js
tripleBacktick = String.fromCharCode(96).repeat(3);
```

<!--{"pinCode":false,"dname":"ce78d5c8-7c07-4e85-82eb-bce6cb751e3d","codeMode":"js","hide":true}-->
```js
getApiKey = async () => {
  const key = GOOGLE_API_KEY;
  if (!key) return null;
  if (typeof key === 'string') return key;
  try {
    if (key.value !== undefined) return (typeof key.value === 'function' ? await key.value() : key.value);
    if (typeof key === 'function') return await key();
    return key.toString();
  } catch (e) {
    console.error("Error resolving API Key:", e);
    return null;
  }
};
```

<!--{"pinCode":false,"dname":"4db4a966-c678-4528-874c-1036ab5351f8","codeMode":"js","hide":true}-->
```js
fetchAvailableModels = async () => {
  console.log("fetchAvailableModels triggered");
  const apiKey = await getApiKey();
  if (!apiKey) {
    gxr.toast().error("Please set GOOGLE_API_KEY first");
    return;
  }
  
  try {
    mutable modelsLoading = true;
    const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models?key=${apiKey}`);
    if (!response.ok) {
      const err = await response.text();
      throw new Error(`Failed to fetch models: ${response.statusText} - ${err}`);
    }
    const data = await response.json();
    
    const geminiModels = (data.models || [])
      .filter(m => m.name && (m.name.includes('gemini') || m.name.includes('gemma')))
      .map(m => ({
        name: m.name.replace('models/', ''),
        displayName: m.displayName || m.name.replace('models/', ''),
        description: m.description || ''
      }))
      .sort((a, b) => {
        const getVer = (n) => {
          const m = n.match(/(\d+\.?\d*)/);
          return m ? parseFloat(m[1]) : 1.0;
        };
        const vA = getVer(a.name);
        const vB = getVer(b.name);
        if (vA !== vB) return vB - vA;
        if (a.name.includes('pro') && !b.name.includes('pro')) return -1;
        if (!a.name.includes('pro') && b.name.includes('pro')) return 1;
        return a.name.localeCompare(b.name);
      });
    
    mutable availableModels = geminiModels;
    
    if (geminiModels.length > 0) {
      if (!selectedModel || !geminiModels.find(m => m.name === selectedModel)) {
        const best = geminiModels.find(m => m.name.includes('flash')) || geminiModels[0];
        mutable selectedModel = best.name;
      }
    }
    
    gxr.toast().success(`Found ${geminiModels.length} models`);
  } catch (error) {
    console.error("Error fetching models:", error);
    gxr.toast().error(`Failed to fetch models: ${error.message}`);
    mutable availableModels = [];
  } finally {
    mutable modelsLoading = false;
  }
};
```

<!--{"pinCode":false,"dname":"4cbff676-d5a5-4e52-968a-f89d5474fd70","codeMode":"js","hide":true}-->
```js
GXR_API_REFERENCE = `# GraphXR API Reference

## Camera

### flyToCenter
Fly the camera to the center of a slice of nodes, optionally with an offset, optionally with a custom duration or tween function
@synonyms centerTo
${tripleBacktick}js
gxr.flyToCenter();
gxr.centerTo(gxr.nodes().slice(0, 10).ids())
gxr.flyToCenter(gxr.nodes().slice(0, 10).ids(), { duration: 0, offset: -3 }));
${tripleBacktick}

### flyToPosition
Fly the camera to a position, optionally with an offset, optionally with a custom duration or tween function
${tripleBacktick}js
const position = gxr.nodes({name: "Flo"}).at(0).position;
gxr.flyToPosition(position); // no offset
gxr.flyToPosition(position, {offset: -3}); // with an offset
gxr.flyToPosition(position, {duration: 0}); // instantly
${tripleBacktick}

### flyOut
Zoom out until all nodes are visible
${tripleBacktick}js
gxr.flyOut();
${tripleBacktick}

## Graph

### addNode
Add node to the graph.
${tripleBacktick}js
gxr.addNode({ id: "A", category: "Person", properties: {name: "Flo"} });
${tripleBacktick}

### addNodes
Add multiple nodes to the graph.
${tripleBacktick}js
gxr.addNodes([{ id: "A", category: "Person" }, { id: "B", category: "Person" }]);
${tripleBacktick}

### addEdge
Add a single edge to the graph.
${tripleBacktick}js
gxr.addEdge({ sourceId: "A", targetId: "B", relationship: "KNOWS" });
gxr.addEdge(nodeA, nodeB);
gxr.addEdge(nodeA, nodeB, { relationship: "KNOWS" });
${tripleBacktick}

### addEdges
Add multiple edges to the graph.
${tripleBacktick}js
gxr.addEdges([{ sourceId: "A", targetId: "B" }, { sourceId: "B", targetId: "C" }]);
${tripleBacktick}

### clear
Clear the graph
${tripleBacktick}js
gxr.clear();
${tripleBacktick}

### edges
${tripleBacktick}js
gxr.edges().forEach(console.log);
gxr.edges().property('since', 2023);
gxr.edges().style('width', 10)
gxr.edges({relationship: 'LINKS'}).style('width', 10);
${tripleBacktick}

### getCanvasSchema
Get schema from the visible nodes and edges on the canvas.
${tripleBacktick}js
const schema = gxr.getCanvasSchema();
console.log(schema.nodes); // [{label: "Person", properties: [...]}]
console.log(schema.relationships); // [{label: "KNOWS", sourceNodeLabel: "Person", targetNodeLabel: "Person", properties: [...]}]
${tripleBacktick}

### nodes
Get an IterableNodes object with an optional filter, sort, or reverse that can be used to iterate over the nodes in the graph.
${tripleBacktick}js
gxr.nodes().forEach(console.log);
gxr.nodes().property('age', 24);
gxr.nodes({category: "Person"}).style('selected', true);
gxr.nodes({properties: {age: 24}}).style('selected', true);
gxr.nodes({properties: {age: (age) => age > 24}}).style('selected', true);
${tripleBacktick}

## Layout

### circle
All the nodes spread on a circle
${tripleBacktick}js
await gxr.circle();
${tripleBacktick}

### grid
Equally space Nodes on the x-axis, y-axis, z=0.
${tripleBacktick}js
await gxr.grid();
${tripleBacktick}

### line
Equally space Nodes on the x-axis, y=0, z=0.
${tripleBacktick}js
await gxr.line();
${tripleBacktick}

### ego
Ego reveals hierarchal data by arranging nodes in a tree, where a node's depth in the tree is equal to the length of the shortest path to the node.
We must select the root nodes before calling ego.
${tripleBacktick}js
await gxr.nodes("a").ego({
  depth: 3,
  edgeLength: 1,
  mode: 'rings', // 'tree' or 'rings'; default 'tree'
  orientation: 'down', // 'up', 'down', 'left', or 'right'; default 'right'
  sortByProperty: 'ComponentName'
}))
${tripleBacktick}

### forceLayout
Run a force layout on the graph.
${tripleBacktick}js
gxr.forceLayout();
${tripleBacktick}

### sphere
All the nodes spread on a sphere using Fibonacci sphere (golden spiral) algorithm
${tripleBacktick}js
await gxr.sphere();
${tripleBacktick}

### spiral
All the nodes spread on a spiral
${tripleBacktick}js
gxr.spiral()
${tripleBacktick}

## Transform

### aggregate
Pull data to root nodes from their neighborhoods up to a certain depth.
${tripleBacktick}js
gxr.aggregate({
  formula: 'sum',
  property: "age",
})
${tripleBacktick}

### extract
${tripleBacktick}js
gxr.extract({
  sourceCategory: "Episodes",
  targetCategory: "Season",
  props: [{ name: "seasonNumber", isKey: true }],
  relationship: "IN_SEASON",
})
${tripleBacktick}

### link
Create new edges between nodes which have matching values for the specified property.
${tripleBacktick}js
gxr.link({ sourceProperty: "id", targetProperty: "id" })
gxr.link({
  sourceCategory: "Person",
  targetCategory: "Person",
  sourceProperty: "email",
  targetProperty: "email",
  relationship: "SAME_EMAIL",
  match: { type: "case-insensitive" }
})
${tripleBacktick}

### merge
Combine nodes or edges which have equivalent key properties.
${tripleBacktick}js
gxr.merge({ keys: ['seasonNumber'] })
${tripleBacktick}

## Analytics

### dijkstra
${tripleBacktick}js
const paths = gxr.dijkstra("A", "B");
gxr.graph(paths).nodes().select()
${tripleBacktick}

### yens
${tripleBacktick}js
const paths = gyen.yens("A", "B");
gxr.graph(paths).nodes().select()
${tripleBacktick}

### traceNeighbor
${tripleBacktick}js
gxr.traceNeighbor()
${tripleBacktick}

## Query

### query
Execute a Cypher query. Results appear in the graph workspace.
${tripleBacktick}js
await gxr.query("MATCH (n) RETURN n LIMIT 10");
${tripleBacktick}

## Styles

### colorNodesByProperty
${tripleBacktick}js
gxr.colorNodesByProperty('age');
gxr.colorNodesByProperty({property: 'age', scale: 'BuGn'});
${tripleBacktick}

## Utility

### dispatchGraphDataUpdate
Force the UI to update.
${tripleBacktick}js
gxr.dispatchGraphDataUpdate();
${tripleBacktick}

### screenshot
Take a screenshot of the graph canvas
${tripleBacktick}js
const blob = await gxr.screenshot({
  frameNodes: false,
  hidePanels: true,
  includeLegends: true,
  format: 'png',
  quality: 1.0
});
${tripleBacktick}
`;
```

<!--{"pinCode":false,"dname":"1120574c-2efc-4aa4-b5dc-d069c5c2e261","codeMode":"js","hide":true}-->
```js
CYPHER_DOCS = `# BigQuery Graph GQL Cheat Sheet

## Schema Notes
Properties are FLAT. Never use .detail.data nesting.
Example: m.year, m.label, m.market_action, m.sector.

## MATCH
Match all nodes and return all nodes.
${tripleBacktick}gql
MATCH (n) RETURN n
${tripleBacktick}

## WHERE
WHERE used to filter on node properties. Use parentheses () for list literals in IN operator.
${tripleBacktick}gql
MATCH (n:Person) WHERE n.id IN ('ID1', 'ID2') RETURN n.name AS name, n.age AS age
${tripleBacktick}

## SHORTEST PATH
BigQuery GQL supports standard path patterns.
${tripleBacktick}gql
MATCH p = (wos:Station)-[:LINK*1..5]->(bmv:Station) 
WHERE wos.name = "Worcester Shrub Hill" 
RETURN p
${tripleBacktick}

## AGGREGATION
avg(), count(), max(), min(), sum()
${tripleBacktick}gql
MATCH (p:Person) RETURN avg(p.age) AS avg_age
${tripleBacktick}

## MULTI-MODAL OPPORTUNITIES (CORRECT FLAT SCHEMA)
${tripleBacktick}gql
MATCH (c:Company)-[r]->(m:Market)
WHERE c.id IN ('PFG')
RETURN m.year AS Year, m.market_action AS Action, m.label AS Opportunity, m.sector AS Sector
ORDER BY Year DESC
LIMIT 100
${tripleBacktick}

## GRAPHXR INTEGRATION
Use gxr.query("...") to execute these.
`;
```

<!--{"pinCode":false,"dname":"a80a2b5a-1795-4f1f-9ac5-c5becea94146","codeMode":"js","hide":true}-->
```js
blobToBase64 = async (blob) => {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onloadend = () => {
      const base64String = reader.result.split(',')[1];
      resolve(base64String);
    };
    reader.onerror = reject;
    reader.readAsDataURL(blob);
  });
};
```

<!--{"pinCode":false,"dname":"4fc0c277-30cd-40a2-b6f2-2a7cde82e84a","codeMode":"js","hide":true}-->
```js
getGraphMetadata = () => {
  try {
    console.log("Safe metadata extraction started (Manual Mode)...");
    const allNodes = gxr.nodes().toArray();
    if (allNodes.length === 0) return null;

    const schema = ({});
    const dataSample = ({});
    const visibleIds = ({});
    const categoriesSet = new Set();

    // Pass 1: Identify categories and build schema from raw data keys
    for (const node of allNodes) {
      if (!node.category) continue;
      categoriesSet.add(node.category);
      
      if (!schema[node.category]) {
        schema[node.category] = ({});
        const rawData = node.data || ({});
        
        // Safety: ensure we are getting the actual property values
        for (const key in rawData) {
          if (!key.startsWith('_') && !key.toLowerCase().includes('fraud')) {
            try {
              const val = rawData[key];
              schema[node.category][key] = typeof val;
            } catch(e) {}
          }
        }
        
        // Take first valid node as data sample
        dataSample[node.category] = [{
          id: node.id,
          category: node.category,
          properties: Object.keys(schema[node.category]).reduce((acc, k) => {
            acc[k] = rawData[k];
            return acc;
          }, ({}))
        }];
        
        // Track a sample of visible IDs for filtering
        visibleIds[node.category] = [];
      }
      
      if (visibleIds[node.category].length < 15) {
        // BUSINESS ID EXTRACTION: Only use the property ID from node.data
        const businessData = node.data || ({});
        const businessId = businessData.id || businessData.ID || businessData.Id;
        
        if (businessId && typeof businessId === 'string' && businessId.length < 50) {
          visibleIds[node.category].push(businessId);
        }
      }
    }
    
    return ({
      categories: Array.from(categoriesSet),
      schema,
      dataSample,
      visibleIds,
      nodeCount: allNodes.length,
      edgeCount: gxr.edges().length
    });
  } catch (error) {
    console.error('Critical error in getGraphMetadata:', error);
    return null;
  }
};
```

<!--{"pinCode":false,"dname":"a614d64e-5e4c-44f0-8efa-4db6f594c54a","codeMode":"js","hide":true}-->
```js
captureScreenshot = async () => {
  try {
    if (!gxr.isCanvasLoaded()) {
      gxr.toast().info("Waiting for canvas to load...");
      await new Promise((resolve) => {
        gxr.onCanvasLoaded(() => resolve());
      });
    }
    
    gxr.toast().info("Capturing graph screenshot...");
    
    const blob = await gxr.screenshot({
      frameNodes: false,
      hidePanels: true,
      includeLegends: true,
      format: 'png',
      quality: 1.0
    });
    
    if (!blob || !blob.size || blob.size === 0) {
      throw new Error("Failed to capture screenshot. The graph canvas may be empty.");
    }
    
    const base64Image = await blobToBase64(blob);
    return ({ blob, base64Image });
  } catch (error) {
    console.error('Error capturing screenshot:', error);
    throw error;
  }
};
```

<!--{"pinCode":false,"dname":"b0c4f38f-28dd-49a5-a5a7-f150b4da93ef","codeMode":"js","hide":true}-->
```js
mutable question = "What patterns can you see for the risks in the graph accross the years?";
```

<!--{"pinCode":false,"dname":"157c988d-ce24-4a2f-ac43-1ba04387b88b","codeMode":"js","hide":true}-->
```js
mutable analysisLoading = false;
```

<!--{"pinCode":false,"dname":"e55640af-7d04-45b9-a06c-d9380ece8859","codeMode":"js","hide":true}-->
```js
mutable analysisError = null;
```

<!--{"pinCode":false,"dname":"61cd4c54-e934-423b-ae75-fabc8961734d","codeMode":"js","hide":true}-->
```js
mutable analysisResult = null;
```

<!--{"pinCode":false,"dname":"35851fe4-7589-4105-9c64-aa67c576e55b","codeMode":"js","hide":true}-->
```js
mutable screenshotUrl = null;
```

<!--{"pinCode":false,"dname":"ae932e17-657e-4ede-9d17-71e4192ec243","codeMode":"js","hide":true}-->
```js
mutable screenshotDataUrl = null;
```

<!--{"pinCode":false,"dname":"cac57f6c-253b-44ed-bfc6-ecd7bb35167b","codeMode":"js","hide":true}-->
```js
mutable streamingText = "";
```

<!--{"pinCode":false,"dname":"0cf9f94f-3f42-43c6-b3ff-14583db7ea13","codeMode":"js","hide":true}-->
```js
mutable pendingActions = [];
```

<!--{"pinCode":false,"dname":"f56204c8-33e1-4a01-ba5a-3560c5adaf41","codeMode":"js","hide":true}-->
```js
mutable queryResults = ({});
```

<!--{"pinCode":false,"dname":"b58f84d1-c13a-4743-9e31-c5ba922f8430","codeMode":"js","hide":true}-->
```js
handleExportPDF = async () => {
  try {
    gxr.toast().info("Generating PDF...");
    
    const exportContainer = document.getElementById('qa-export-container');
    if (!exportContainer) {
      throw new Error("Analysis card not available.");
    }
    
    const { jsPDF } = await import('https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js');
    const html2canvasModule = await import('https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js');
    const html2canvas = html2canvasModule.default || html2canvasModule;
    if (typeof window !== 'undefined') {
      window.html2canvas = html2canvas;
    }
    
    const clone = exportContainer.cloneNode(true);
    const cloneExportBtn = clone.querySelector('#export-pdf-btn');
    if (cloneExportBtn) cloneExportBtn.remove();
    const styleEl = document.createElement('style');
    const c = theme.colors;
    const r = theme.borderRadius;
    const computedExport = window.getComputedStyle(exportContainer);
    const computedBody = window.getComputedStyle(document.body);
    const exportBg = computedExport.backgroundColor || c.bgCardLight;
    const exportBorder = computedExport.borderColor || c.borderPrimary;
    const exportText = computedExport.color || c.textPrimary;
    const headerColor = (exportContainer.querySelector('h3') && window.getComputedStyle(exportContainer.querySelector('h3')).color) || c.textSecondary;
    const bodyBg = computedBody.backgroundColor || c.bgPrimary;
    styleEl.textContent = `
      body { background: ${bodyBg} !important; color: ${exportText} !important; }
      #qa-export-container {
        background: ${exportBg} !important;
        border: 2px solid ${exportBorder} !important;
        border-radius: ${r.section} !important;
        color: ${exportText} !important;
      }
      #qa-export-container h3 { color: ${headerColor} !important; }
      #qa-export-container .qa-answer-content { color: ${exportText} !important; }
      #qa-export-container .qa-answer-content strong,
      #qa-export-container .qa-answer-content b {
        color: ${headerColor} !important;
        font-weight: 700 !important;
      }
      #qa-export-container .qa-answer-content h1,
      #qa-export-container .qa-answer-content h2,
      #qa-export-container .qa-answer-content h3,
      #qa-export-container .qa-answer-content h4,
      #qa-export-container .qa-answer-content h5,
      #qa-export-container .qa-answer-content h6 { color: ${headerColor} !important; }
      #qa-export-container .qa-answer-content blockquote {
        border-left: 3px solid ${exportBorder} !important;
        color: ${exportText} !important;
      }
      #qa-export-container table { color: ${exportText} !important; }
      #qa-export-container th { color: ${headerColor} !important; }
      #qa-export-container td { color: ${exportText} !important; }
    `;
    clone.prepend(styleEl);
    clone.style.position = 'absolute';
    clone.style.left = '-9999px';
    clone.style.top = '0';
    clone.style.width = `${exportContainer.offsetWidth}px`;
    clone.style.boxShadow = 'none';
    clone.style.transform = 'scale(1)';
    document.body.appendChild(clone);
    
    const pdf = new jsPDF({
      orientation: 'p',
      unit: 'pt',
      format: 'a4'
    });
    
    await pdf.html(clone, {
      margin: [0],
      autoPaging: 'text',
      html2canvas: {
        scale: Math.min(2, window.devicePixelRatio || 1.5),
        useCORS: true,
        backgroundColor: bodyBg
      }
    });
    
    document.body.removeChild(clone);
    
    const timestamp = new Date().toISOString().slice(0, 10);
    pdf.save(`graph-analysis-${timestamp}.pdf`);
    
    gxr.toast().success("PDF exported successfully!");
  } catch (error) {
    console.error("Error exporting PDF:", error);
    
    try {
      const exportContainer = document.getElementById('qa-export-container');
      if (!exportContainer) throw new Error("Analysis card not available.");
      const c = theme.colors;
      const r = theme.borderRadius;
      const bodyBg = c.bgPrimary;
      const exportBg = c.bgCardLight;
      const exportBorder = c.borderPrimary;
      const exportText = c.textPrimary;

      const exportHtml = (() => {
        const tmp = exportContainer.cloneNode(true);
        const btn = tmp.querySelector('#export-pdf-btn');
        if (btn) btn.remove();
        return tmp.innerHTML;
      })();
      
      const printWindow = window.open('', '_blank');
      printWindow.document.write(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>Graph Analysis Report</title>
          <style>
            body { margin: 0; padding: 40px; background: ${bodyBg}; color: ${exportText}; font-family: 'Segoe UI', sans-serif; }
            .wrapper { max-width: 900px; margin: 0 auto; background: ${exportBg}; border: 2px solid ${exportBorder}; border-radius: ${r.section}; padding: 24px; color: ${exportText}; }
          </style>
        </head>
        <body>
          <div class="wrapper">${exportHtml}</div>
        </body>
        </html>
      `);
      printWindow.document.close();
      printWindow.print();
    } catch (fallbackError) {
      console.error("Fallback print failed:", fallbackError);
      gxr.toast().error("Failed to export PDF. Try browser print (Ctrl+P).");
    }
  }
};
```

<!--{"pinCode":false,"dname":"37e94c00-91d0-42da-a38c-469be8828f16","codeMode":"js","hide":true}-->
```js
askVisualAgent = async () => {
  console.log("askVisualAgent triggered");
  if (analysisLoading) return;
  
  const questionInput = document.getElementById('question-input');
  const currentText = questionInput ? questionInput.value : question;
  
  if (!currentText.trim()) {
    gxr.toast().error("Please enter a question");
    return;
  }

  const apiKey = await getApiKey();
  if (!apiKey) {
    gxr.toast().error("Please set GOOGLE_API_KEY first");
    return;
  }
  
  if (!selectedModel) {
    gxr.toast().error("Please select a model first");
    return;
  }
  
  try {
    mutable analysisLoading = true;
    mutable analysisError = null;
    mutable analysisResult = null;
    mutable streamingText = "";
    mutable pendingActions = [];
    mutable queryResults = ({});
    
    // Sync the mutable question state
    mutable question = currentText;

    let base64Image = null;
    if (gxr.nodes().length > 0) {
      const screenshotResult = await captureScreenshot();
      base64Image = screenshotResult.base64Image;
      if (screenshotUrl && screenshotUrl.startsWith('blob:')) URL.revokeObjectURL(screenshotUrl);
      mutable screenshotUrl = URL.createObjectURL(screenshotResult.blob);
      mutable screenshotDataUrl = `data:image/png;base64,${base64Image}`;
    }
    
    const metadata = getGraphMetadata();
    const schemaAndData = metadata ? 
      `Graph Context:\n\nSchema:\n${JSON.stringify(metadata.schema, null, 2)}\n\nVisible Business IDs (Use these for WHERE node.id IN (...)): ${JSON.stringify(metadata.visibleIds)}\n\nSample Data:\n${JSON.stringify(metadata.dataSample, null, 2)}` :
      'No graph data available.';
    
    gxr.toast().info("Submitting to AI...");
    
    const systemPrompt = `You are an expert graph data analyst for BigQuery Graph. Analyze the provided graph screenshot and metadata. Provide a response that contains a JSON block with the following structure: {"analysis": "text markdown", "actions": [{"explanation": "text", "gql": "MATCH... RETURN..."}]}. 

CRITICAL RULES:
1. STRICT PROPERTY NAMES: Use ONLY property names found in the provided 'Schema' and 'Sample Data'. NEVER invent names like 'competitorLabel' or 'competitorKey'. Use 'label' instead.
2. PROPERTIES ARE FLAT: NEVER use ".detail.data" nesting. Access properties directly, e.g., m.year, m.label, m.sector.
3. USE BigQuery Graph GQL compatible syntax.
4. IN OPERATOR: Use parentheses () for list literals in IN operator. Example: WHERE node.id IN ('ID1', 'ID2').
5. BUSINESS IDs ONLY: Use ONLY values from "Visible Business IDs" for filters.
6. NO COMMANDS: DO NOT provide a "commands" array.
7. ANALYTICAL INSIGHTS: Provide insights NOT easily seen in the graph.
8. LIMIT: Include LIMIT 100 in all GQL queries.`;

    const userPrompt = `Question: ${currentText}\n\n${schemaAndData}`;

    const modelName = selectedModel.startsWith('models/') ? selectedModel : `models/${selectedModel}`;
    const url = `https://generativelanguage.googleapis.com/v1beta/${modelName}:streamGenerateContent?key=${apiKey}`;
    
    const response = await fetch(url, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        contents: [{
          role: "user",
          parts: [
            { text: systemPrompt + "\n\n" + userPrompt },
            ...(base64Image ? [{ inlineData: { mimeType: "image/png", data: base64Image } }] : [])
          ]
        }],
        generationConfig: { temperature: 0.7, maxOutputTokens: 8192 }
      })
    });
    
    if (!response.ok) throw new Error(await response.text());
    
    const reader = response.body.getReader();
    const decoder = new TextDecoder();
    let fullText = '';
    
    while (true) {
      const { done, value } = await reader.read();
      if (done) break;
      
      const chunk = decoder.decode(value, { stream: true });
      const textMatches = chunk.match(/"text":\s*"((?:[^"\\]|\\.)*)"/g);
      if (textMatches) {
        for (const m of textMatches) {
          try {
            const strValue = m.match(/"text":\s*"(.*)"/)[1];
            const decoded = JSON.parse('"' + strValue + '"');
            fullText += decoded;
            mutable streamingText = fullText;
          } catch (e) {}
        }
      }
    }
    
    let parsedResult = null;
    try {
      const jsonMatch = fullText.match(new RegExp(`${tripleBacktick}(?:json)?\\s*(\\{[\\s\\S]*?\\})\\s*${tripleBacktick}`)) || 
                        fullText.match(/(\{[\s\S]*"analysis"[\s\S]*\})/);
      if (jsonMatch) {
        parsedResult = JSON.parse(jsonMatch[1]);
      }
    } catch (e) {
      console.warn("JSON parse failed, using raw text fallback", e);
    }
    
    if (parsedResult?.actions) {
      mutable analysisResult = parsedResult.analysis;
      mutable pendingActions = parsedResult.actions.map(a => ({
        explanation: a.explanation,
        cypher: a.gql || a.cypher // Normalize gql field
      }));
    } else {
      mutable analysisResult = fullText;
    }
    
    gxr.toast().success("Analysis complete!");
  } catch (error) {
    console.error("Agent Error:", error);
    mutable analysisError = error.message;
    gxr.toast().error("Failed: " + error.message);
  } finally {
    mutable analysisLoading = false;
  }
};
```

<!--{"pinCode":false,"dname":"00bcaa6a-054e-465b-945b-92165095e409","codeMode":"js","hide":true}-->
```js
executeGxrCommands = async (commands) => {
  if (!commands || commands.length === 0) {
    gxr.toast().warning("No commands to execute");
    return;
  }
  
  try {
    gxr.toast().info("Executing GraphXR commands...");
    
    const commandGroups = commands
      .join('; ')
      .split(';;')
      .map(group => group.trim())
      .filter(group => group.length > 0);
    
    for (const group of commandGroups) {
      const groupCommands = group.split(';')
        .map(cmd => cmd.trim())
        .filter(cmd => cmd.length > 0);
      
      if (groupCommands.length === 0) continue;
      
      const hasAwait = groupCommands.some(cmd => /\bawait\b/.test(cmd));
      const hasVars = groupCommands.some(cmd => /^\s*(const|let|var)\s+/.test(cmd));
      
      const commandBlock = groupCommands
        .map(cmd => cmd.trim().endsWith(';') ? cmd.trim() : cmd.trim() + ';')
        .join('\n');
      
      if (hasAwait || hasVars) {
        const asyncCommand = `(async () => { ${commandBlock} })()`;
        await eval(asyncCommand);
      } else {
        const asyncCommand = `(async () => { ${commandBlock} })()`;
        await eval(asyncCommand);
      }
    }
    
    // gxr.dispatchGraphDataUpdate();
    gxr.toast().success("Commands executed successfully!");
  } catch (error) {
    console.error("Error executing commands:", error);
    gxr.toast().error(`Command execution failed: ${error.message}`);
    throw error;
  }
};
```

<!--{"pinCode":false,"dname":"9d5c1544-d85e-42ec-b327-a62380400171","codeMode":"js","hide":true}-->
```js
executeCypher = async (query, index) => {
  console.log(`executeCypher triggered for action ${index} with query:`, query);
  if (!query || !query.trim()) {
    gxr.toast().warning("No query to execute");
    return;
  }
  
  try {
    gxr.toast().info("Running query...");
    const rawResult = await gxr.query(query, {saveToGraph: false});
    console.log("Query raw result:", rawResult);
    
    let result = null;
    let columns = null;
    
    if (rawResult && rawResult._content) {
      result = rawResult._content.data || [];
      columns = rawResult._content.columns || null;
    } else if (Array.isArray(rawResult)) {
      result = rawResult;
    }
    
    if (result && result.length > 0 && Array.isArray(result[0])) {
      const firstRow = result[0];
      const allStrings = firstRow.every(v => v !== null && v !== undefined && typeof v === 'string');
      const isHeaderRow = allStrings && firstRow.some(v => 
        ['year', 'label', 'sector', 'action', 'market', 'company', 'id', 'count', 'confidence'].some(k => v.toLowerCase().includes(k))
      );
      
      const columnsAreNumeric = !columns || columns.every(c => !isNaN(parseInt(c)));

      if (isHeaderRow && columnsAreNumeric) {
        columns = firstRow;
        result = result.slice(1);
      }
    }

    if (columns) {
      const data = result || [];
      const mappedResult = data.slice(0, 100).map(row => {
        const obj = {};
        columns.forEach((col, idx) => {
          obj[col] = row[idx];
        });
        return obj;
      });
      
      const newResults = ({ ...queryResults });
      newResults[index] = { columns, data: mappedResult };
      mutable queryResults = newResults;
      gxr.toast().success(`Results for Action ${index + 1} updated`);
    } else if (result && result.length > 0) {
      const first = result[0];
      if (typeof first === 'object' && first !== null && !first._isNode && !first._isEdge) {
        const cols = Object.keys(first);
        const newResults = ({ ...queryResults });
        newResults[index] = { columns: cols, data: result.slice(0, 100) };
        mutable queryResults = newResults;
        gxr.toast().success(`Results for Action ${index + 1} updated`);
      }
    }
    
    // gxr.dispatchGraphDataUpdate();
  } catch (error) {
    console.error("Error executing query:", error);
    gxr.toast().error(`Query failed: ${error.message}`);
    throw error;
  }
};
```

<!--{"pinCode":false,"dname":"02b91c85-5d2c-48fb-a41e-e740b46354d3","codeMode":"js","hide":true}-->
```js
executeAction = async (action, index) => {
  try {
    if (action.cypher) {
      await executeCypher(action.cypher, index);
    }
    gxr.toast().success(`Action ${index + 1} executed successfully!`);
  } catch (error) {
    gxr.toast().error(`Action ${index + 1} failed: ${error.message}`);
  }
};
```

<!--{"pinCode":false,"dname":"0ae9a26e-2699-409e-8065-2559ebae4d5a","codeMode":"js","hide":true}-->
```js
{
  if (typeof window !== 'undefined') {
    window.fetchAvailableModels = fetchAvailableModels;
    window.askVisualAgent = askVisualAgent;
    window.executeCypher = executeCypher;
    window.executeGxrCommands = executeGxrCommands;
    window.executeAction = executeAction;
    window.handleExportPDF = handleExportPDF;
  }
}
```

<!--{"pinCode":false,"dname":"97f4dbb4-2949-449e-8ee2-2d9103fb5ab8","codeMode":"js","hide":true}-->
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

<!--{"pinCode":false,"dname":"06981231-daa8-4684-a124-27aee5fa8e99","codeMode":"js","hide":true}-->
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

<!--{"pinCode":false,"dname":"0889ff96-f660-43f4-9be0-6f6c0ab28d68","codeMode":"js","hide":true}-->
```js
mutable selectedTheme = 'dark'
```

<!--{"pinCode":false,"dname":"d7e5514b-41a4-4fe6-8ba0-59fb778ca3e1","codeMode":"js","hide":true}-->
```js
// Active theme based on selection
theme = selectedTheme === 'dark' ? theme_dark : theme_light
```

<!--{"pinCode":false,"dname":"449f5a78-94f6-4019-83c9-ade3e66ed451","codeMode":"js"}-->
```js
{
  const c = theme.colors;
  const s = theme.spacing;
  const t = theme.typography;
  const r = theme.borderRadius;
  const sh = theme.shadows;
  const tr = theme.transitions;
  const g = theme.gradients;
  
  // Internal state for collapsible config
  let configExpanded = false;
  
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
  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: ${s.gapLarge};">
    <h2 style="margin: 0; font-size: ${t.fontSizeHeading}; color: ${c.textSecondary};">
      Visual Analysis Agent
    </h2>
  </div>
  
  <!-- Collapsible Configuration Section -->
  <div style="margin-bottom: ${s.sectionMargin}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderSecondary};">
    <div style="display: flex; justify-content: space-between; align-items: center; cursor: pointer;" id="config-header">
      <h3 style="margin: 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">Configuration</h3>
      <span id="config-toggle-icon" style="color: ${c.textTertiary}; font-size: 1.2em; transition: ${tr.default};">▼</span>
    </div>
    
    <div id="config-content" style="display: none; margin-top: ${s.gapLarge};">
      <div style="margin-bottom: ${s.gapLarge};">
        <label style="display: block; color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; font-size: ${t.fontSizeSmall};">Google API Key:</label>
        <input id="key-input" type="password" value="${GOOGLE_API_KEY && GOOGLE_API_KEY.value ? GOOGLE_API_KEY.value : (typeof GOOGLE_API_KEY === 'string' ? GOOGLE_API_KEY : '')}" 
          style="width: 100%; padding: ${s.inputPadding}; background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; font-size: ${t.fontSizeBody}; box-sizing: border-box; outline: none; transition: ${tr.border};" 
          onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'" />
      </div>
      
      <div style="margin-bottom: ${s.gapLarge};">
        <div style="display: flex; gap: ${s.gapSmall}; align-items: center; margin-bottom: ${s.gapSmall};">
          <label style="color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; font-size: ${t.fontSizeSmall};">Model:</label>
          <button id="refresh-btn" style="padding: ${s.buttonPaddingSmall}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.buttonSmall}; font-size: ${t.fontSizeTiny}; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.background};" 
            onmouseover="this.style.background='${c.btnPrimaryHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'">
            ${modelsLoading ? 'Loading...' : '🔄 Refresh Models'}
          </button>
        </div>
        <select id="model-select" style="width: 100%; padding: ${s.inputPadding}; background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; font-size: ${t.fontSizeBody}; outline: none; transition: ${tr.border}; cursor: pointer;"
          onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'">
          ${availableModels.length === 0 ? html`<option value="">No models available - Click Refresh</option>` : 
            availableModels.map(model => html`<option value="${model.name}" ${model.name === selectedModel ? 'selected' : ''}>${model.displayName}</option>`)}
        </select>
      </div>
    </div>
  </div>
  
  <div style="margin-bottom: ${s.gapLarge};">
    <label style="display: block; color: ${c.textTertiary}; font-weight: ${t.fontWeightSemiBold}; margin-bottom: ${s.gapSmall}; font-size: ${t.fontSizeSmall};">Question:</label>
    <textarea id="question-input" placeholder="e.g., What Market opportunities can you see for the company in the graph accross the years?" 
      style="width: 100%; min-height: 100px; padding: ${s.inputPadding}; background: ${c.bgInput}; border: 2px solid ${c.borderPrimary}; border-radius: ${r.input}; color: ${c.textPrimary}; font-size: ${t.fontSizeBody}; resize: vertical; box-sizing: border-box; font-family: ${t.fontFamily}; outline: none; transition: ${tr.border};"
      onfocus="this.style.borderColor='${c.borderFocus}'" onblur="this.style.borderColor='${c.borderPrimary}'"
    >${question}</textarea>
  </div>
  
  <button id="analyze-btn" style="width: 100%; padding: ${s.buttonPadding}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.button}; font-size: 1.1em; font-weight: ${t.fontWeightBold}; cursor: pointer; transition: ${tr.background}; box-shadow: ${sh.button};"
    onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">
    ${analysisLoading ? 'Analyzing...' : 'Analyze Graph'}
  </button>

  ${analysisError ? html`<div style="margin-top: ${s.gapLarge}; padding: ${s.inputPadding}; background: rgba(255, 82, 82, 0.1); border: 1px solid #ff5252; border-radius: ${r.input}; color: #ff5252;"><strong>Error:</strong> ${analysisError}</div>` : ''}
  
  ${analysisLoading && streamingText ? html`<div style="margin-top: ${s.gapLarge}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 1px solid ${c.borderTertiary}; max-height: 300px; overflow-y: auto;"><div style="color: ${c.textTertiary}; font-weight: ${t.fontWeightBold}; margin-bottom: ${s.gapSmall};">Streaming Response:</div><pre style="color: ${c.textPrimary}; font-size: ${t.fontSizeMicro}; white-space: pre-wrap; margin: 0;">${streamingText}</pre></div>` : ''}
  
  ${analysisResult || pendingActions.length > 0 ? html`
    <div id="qa-export-container" style="margin-top: ${s.gapLarge}; padding: ${s.sectionPadding}; background: ${c.bgCardLight}; border: 2px solid ${c.borderSecondary}; border-radius: ${r.section}; width: 100%; box-sizing: border-box;">
      <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: ${s.gapMedium};">
        <h3 style="margin: 0; color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading};">Analysis Results</h3>
        <button id="export-pdf-btn" style="background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; padding: ${s.buttonPaddingSmall}; border-radius: ${r.buttonSmall}; cursor: pointer; font-size: ${t.fontSizeTiny}; font-weight: ${t.fontWeightBold}; transition: ${tr.background}; box-shadow: ${sh.button};"
          onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">
          Export PDF
        </button>
      </div>

      <div style="margin-bottom: ${s.gapLarge}; padding: ${s.inputPadding}; background: ${c.bgCard}; border-radius: ${r.input}; border-left: 4px solid ${c.borderPrimary};">
        <div style="color: ${c.textTertiary}; font-size: ${t.fontSizeMicro}; font-weight: ${t.fontWeightBold}; margin-bottom: 4px; text-transform: uppercase;">User Question:</div>
        <div style="color: ${c.textPrimary}; font-size: ${t.fontSizeBody}; font-style: italic;">"${question}"</div>
      </div>
      
      ${screenshotUrl ? html`<div style="margin-bottom: ${s.gapLarge}; width: 100%;"><div style="color: ${c.textTertiary}; font-size: ${t.fontSizeSmall}; font-weight: ${t.fontWeightBold}; margin-bottom: ${s.gapSmall};">Graph Screenshot</div><img src="${screenshotUrl}" style="width: 100%; height: auto; display: block; border-radius: ${r.input}; border: 1px solid ${c.borderTertiary};" /></div>` : ''}
      
      <div class="qa-answer-content">
        ${analysisResult ? html`<div style="margin-bottom: ${s.gapLarge}; padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.input}; border-left: 4px solid ${c.textTertiary};"><div class="analysis-text" style="color: ${c.textPrimary}; line-height: 1.6; white-space: pre-wrap; font-size: ${t.fontSizeBody};">${md`${analysisResult}`}</div></div>` : ''}
        
        ${pendingActions.length > 0 ? html`<div><div style="color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading}; font-weight: ${t.fontWeightBold}; margin-bottom: ${s.gapMedium};">Suggested Actions (${pendingActions.length})</div><div style="display: grid; gap: ${s.gapMedium};">
          ${pendingActions.map((action, index) => {
            const result = queryResults[index];
            return html`
              <div style="padding: ${s.sectionPadding}; background: ${c.bgCard}; border-radius: ${r.section}; border: 2px solid ${c.borderSecondary}; box-sizing: border-box;">
                <div style="color: ${c.textTertiary}; font-size: ${t.fontSizeSubheading}; margin-bottom: ${s.gapMedium}; font-weight: ${t.fontWeightSemiBold};">Action ${index + 1}: ${action.explanation}</div>
                ${action.cypher ? html`<div style="margin-bottom: ${s.gapMedium};"><div style="color: ${c.textTertiary}; font-size: ${t.fontSizeMicro}; font-weight: ${t.fontWeightBold}; margin-bottom: 4px;">GQL (BigQuery Compatible):</div><textarea id="cypher-${index}" style="width: 100%; min-height: 100px; padding: ${s.inputPadding}; background: ${c.bgInput}; border: 1px solid ${c.borderTertiary}; border-radius: ${r.input}; color: ${c.textPrimary}; font-family: 'Monaco','Menlo',monospace; font-size: ${t.fontSizeMicro}; resize: vertical; box-sizing: border-box;">${action.cypher}</textarea>
                <button class="run-query-btn" data-index="${index}" style="margin-top: ${s.gapSmall}; width: 100%; padding: ${s.inputPadding}; background: ${c.btnPrimary}; color: ${c.textPrimary}; border: none; border-radius: ${r.input}; cursor: pointer; font-weight: ${t.fontWeightBold}; font-size: ${t.fontSizeSmall}; transition: ${tr.background}; box-shadow: ${sh.button};"
                  onmouseover="this.style.background='${c.btnPrimaryHover}'; this.style.boxShadow='${sh.buttonHover}'" onmousedown="this.style.background='${c.btnPrimaryActive}'" onmouseup="this.style.background='${c.btnPrimaryHover}'" onmouseout="this.style.background='${c.btnPrimary}'; this.style.boxShadow='${sh.button}'">Run Query</button></div>` : ''}
                
                ${result ? html`
                  <div style="margin-top: ${s.gapLarge}; padding: ${s.sectionPadding}; background: ${c.bgCardLight}; border: 1px solid ${c.borderTertiary}; border-radius: ${r.input};">
                    <div style="color: ${c.textTertiary}; font-weight: ${t.fontWeightBold}; margin-bottom: ${s.gapMedium}; display: flex; justify-content: space-between; align-items: center;">
                      <span style="font-size: ${t.fontSizeSmall};">Results for Action ${index + 1}</span>
                      <button class="clear-action-btn" data-index="${index}" style="background: none; border: none; color: #ff5252; cursor: pointer; font-size: ${t.fontSizeMicro}; padding: 0;">Clear</button>
                    </div>
                    <div style="overflow: auto; max-height: 240px;">
                      <table style="width: 100%; border-collapse: collapse; color: ${c.textPrimary}; font-size: ${t.fontSizeMicro};">
                        <thead>
                          <tr style="border-bottom: 2px solid ${c.borderSecondary};">
                            ${result.columns.map(key => html`<th style="padding: ${s.gapSmall}; text-align: left; color: ${c.textTertiary};">${key}</th>`)}
                          </tr>
                        </thead>
                        <tbody>
                          ${result.data.length > 0 ? result.data.map(row => html`
                            <tr style="border-bottom: 1px solid ${c.borderTertiary};">
                              ${Object.values(row).map(val => html`<td style="padding: ${s.gapSmall};">${val === null ? 'null' : (typeof val === 'object' ? JSON.stringify(val) : val)}</td>`)}
                            </tr>
                          `) : html`<tr><td colspan="${result.columns.length}" style="padding: ${s.sectionPadding}; text-align: center; color: ${c.textTertiary};">No results found.</td></tr>`}
                        </tbody>
                      </table>
                    </div>
                  </div>
                ` : ''}
              </div>`;
          })}
        </div></div>` : ''}
      </div>
    </div>` : ''}
</div>`;

  // Event listeners
  // Collapsible config section
  const configHeader = container.querySelector('#config-header');
  const configContent = container.querySelector('#config-content');
  const configIcon = container.querySelector('#config-toggle-icon');
  
  configHeader.onclick = () => {
    configExpanded = !configExpanded;
    configContent.style.display = configExpanded ? 'block' : 'none';
    configIcon.textContent = configExpanded ? '▲' : '▼';
    configIcon.style.transform = configExpanded ? 'rotate(0deg)' : 'rotate(0deg)';
  };
  
  // Form inputs
  container.querySelector('#key-input').oninput = (e) => { mutable GOOGLE_API_KEY = e.target.value; };
  container.querySelector('#model-select').onchange = (e) => { mutable selectedModel = e.target.value; };
  container.querySelector('#refresh-btn').onclick = fetchAvailableModels;
  container.querySelector('#analyze-btn').onclick = askVisualAgent;
  
  const exportBtn = container.querySelector('#export-pdf-btn');
  if (exportBtn) exportBtn.onclick = handleExportPDF;

  container.querySelectorAll('.run-query-btn').forEach(btn => {
    btn.onclick = () => {
      const idx = btn.getAttribute('data-index');
      const query = container.querySelector(`#cypher-${idx}`).value;
      executeCypher(query, parseInt(idx));
    };
  });

  container.querySelectorAll('.clear-action-btn').forEach(btn => {
    btn.onclick = () => {
      const idx = btn.getAttribute('data-index');
      const newResults = ({ ...queryResults });
      delete newResults[idx];
      mutable queryResults = newResults;
    };
  });
  
  if (modelsLoading) container.querySelector('#refresh-btn').disabled = true;
  if (analysisLoading || !selectedModel) container.querySelector('#analyze-btn').disabled = true;

  return container;
}
```
