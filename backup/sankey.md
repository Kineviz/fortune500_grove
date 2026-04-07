<!--{"pinCode":false,"dname":"6a9e4662-5743-416f-ab6a-7fa1295aac1b","codeMode":"js"}-->
# SEC Filings Sankey Diagram

Visualizing company transitions through Risk, Opportunity, and Market categories across years 2020-2024.

<!--{"pinCode":false,"dname":"5c4e3bff-9fd3-4283-9f73-5271b6d72486","codeMode":"js","hide":true}-->
```js
mutable selectedDimension = "Risk"
```

<!--{"pinCode":false,"dname":"061ca894-2239-45cc-89b4-fac9fb7bc7fe","codeMode":"js"}-->
```js
viewof selectedDimensionComboBox = Inputs.select(
  ["Risk", "Opportunity", "Market"],
  { 
    value: selectedDimension,
    label: "Dimension:"
  }
)
```

<!--{"pinCode":false,"dname":"5c09e813-4675-4e3e-b365-ba7b84e67fe2","codeMode":"js","hide":true}-->
```js
// Auto-load data when dimension changes
data = (async () => {
  // Map dimension to relationship types
  const dimensionToRelTypes = {
    'Risk': ['FACES_RISK'],
    'Opportunity': ['PURSUING'],
    'Market': ['ENTERING', 'EXPANDING', 'EXITING']
  };
  
  const relTypes = dimensionToRelTypes[selectedDimensionComboBox] || dimensionToRelTypes['Risk'];
  
  // Build query parts for each relationship type
  const queryParts = relTypes.map((rt, index) => {
    if (index === 0) {
      return `MATCH (c:Company)-[:${rt}]->(target)
    WHERE target.year >= 2020 AND target.year <= 2024
    RETURN 
      c.label AS company,
      target.year AS year,
      '${rt}' AS relType,
      labels(target)[0] AS category,
      target.label AS targetLabel`;
    } else {
      return `UNION
    MATCH (c:Company)-[:${rt}]->(target)
    WHERE target.year >= 2020 AND target.year <= 2024
    RETURN 
      c.label AS company,
      target.year AS year,
      '${rt}' AS relType,
      labels(target)[0] AS category,
      target.label AS targetLabel`;
    }
  });
  
  const query = queryParts.join('\n') + '\n    ORDER BY company, year';
  console.log('Loading data for dimension:', selectedDimensionComboBox);
  console.log(query);
  
  const result = await gxr.query(query, { saveToGraph: false });
  console.log('Data loaded:', result);
  return result;
})()
```

<!--{"pinCode":false,"dname":"b6e43c82-0c7a-4207-aae2-5de719a2643a","codeMode":"js","hide":true}-->
```js
availableCompanies = (async () => {
  const rawData = await data;
  const companies = new Set();
  
  if (rawData && rawData.data && Array.isArray(rawData.data) && rawData.data.length > 0) {
    // Skip the first row (header) and process data rows
    for (let i = 1; i < rawData.data.length; i++) {
      const row = rawData.data[i];
      if (!Array.isArray(row) || row.length < 5) continue;
      const company = row[0];
      if (company) {
        companies.add(company);
      }
    }
  }
  
  const sortedCompanies = Array.from(companies).sort();
  // Add "Select All" as the first option
  return ["Select All", ...sortedCompanies];
})()
```

<!--{"pinCode":false,"dname":"3898fe73-068e-4806-8f4d-7129ea4cad17","codeMode":"js"}-->
```js
viewof selectedCompanies = Inputs.select(
  availableCompanies,
  {
    label: "Companies:",
    multiple: true,
    value: [],
    search: true
  }
)

<!--{"pinCode":false,"dname":"clear-selection-button","codeMode":"javascript2","hide":false}-->
```

<!--{"pinCode":false,"dname":"6d4f53de-8b0c-491c-b6f4-49e9358e1719","codeMode":"js","hide":true}-->
```js
sankeyData = (async () => {
  const rawData = await data;
  const dimension = selectedDimensionComboBox;
  const selected = await selectedCompanies;
  
  // State mapping: relationship types to Sankey states
  const stateMap = {
    'FACES_RISK': 'Risk',
    'PURSUING': 'Opportunity',
    'ENTERING': 'Entering',
    'EXPANDING': 'Expanding',
    'EXITING': 'Exiting'
  };
  
  // States to show based on dimension
  let states;
  if (dimension === 'Risk') {
    states = ['Risk'];
  } else if (dimension === 'Opportunity') {
    states = ['Opportunity'];
  } else if (dimension === 'Market') {
    states = ['Entering', 'Expanding', 'Exiting'];
  } else {
    states = ['Risk'];
  }
  
  const years = [2020, 2021, 2022, 2023, 2024];
  
  // For Market dimension, we need to track relType separately to distinguish Entering/Expanding/Exiting
  // For other dimensions, relType is the same for all entries
  const isMarketDimension = dimension === 'Market';
  
  // Build node map: company -> year -> (relType -> targetLabel) for Market, or company -> year -> targetLabel for others
  const companyYearLabels = new Map(); // company -> year -> Set of targetLabels (or Map of relType -> Set of targetLabels for Market)
  const yearTargetLabels = new Map(); // year -> Set of targetLabels (or Map of relType -> Set of targetLabels for Market)
  
  // Filter companies: if "Select All" is selected or none selected, show all; otherwise filter
  const selectedCompaniesSet = selected && selected.length > 0 && !selected.includes("Select All")
    ? new Set(selected) 
    : null; // null means show all
  
  if (rawData && rawData.data && Array.isArray(rawData.data) && rawData.data.length > 0) {
    // Skip the first row (header) and process data rows
    for (let i = 1; i < rawData.data.length; i++) {
      const row = rawData.data[i];
      if (!Array.isArray(row) || row.length < 5) continue; // Need at least 5 columns: company, year, relType, category, targetLabel
      
      const company = row[0];  // company
      const year = row[1];      // year
      const relType = row[2];   // relType
      const category = row[3];  // category
      const targetLabel = row[4]; // targetLabel
      const state = stateMap[relType];
      
      if (!company || !year || !state || !targetLabel) continue;
      
      // Filter by selected companies
      if (selectedCompaniesSet && !selectedCompaniesSet.has(company)) {
        continue;
      }
      
      // Track company -> year -> targetLabel relationships
      // For Market, also track relType to distinguish states
      if (!companyYearLabels.has(company)) {
        companyYearLabels.set(company, new Map());
      }
      const yearMap = companyYearLabels.get(company);
      if (!yearMap.has(year)) {
        if (isMarketDimension) {
          yearMap.set(year, new Map()); // Map of relType -> Set of targetLabels
        } else {
          yearMap.set(year, new Set()); // Set of targetLabels
        }
      }
      
      if (isMarketDimension) {
        // For Market: track relType -> targetLabel
        const relTypeMap = yearMap.get(year);
        if (!relTypeMap.has(relType)) {
          relTypeMap.set(relType, new Set());
        }
        relTypeMap.get(relType).add(targetLabel);
      } else {
        // For Risk/Opportunity: just track targetLabel
        yearMap.get(year).add(targetLabel);
      }
      
      // Track all targetLabels per year for node creation
      if (!yearTargetLabels.has(year)) {
        if (isMarketDimension) {
          yearTargetLabels.set(year, new Map()); // Map of relType -> Set of targetLabels
        } else {
          yearTargetLabels.set(year, new Set()); // Set of targetLabels
        }
      }
      
      if (isMarketDimension) {
        // For Market: track relType -> targetLabel
        const relTypeMap = yearTargetLabels.get(year);
        if (!relTypeMap.has(relType)) {
          relTypeMap.set(relType, new Set());
        }
        relTypeMap.get(relType).add(targetLabel);
      } else {
        // For Risk/Opportunity: just track targetLabel
        yearTargetLabels.get(year).add(targetLabel);
      }
    }
  }
  
  // Generate nodes: companies + year-targetLabel combinations
  const nodes = [];
  const nodeIndex = new Map();
  
  // Add company nodes (column 0) - only for filtered companies
  const companies = Array.from(companyYearLabels.keys()).sort();
  companies.forEach(company => {  
    const id = `company-${company}`;
    nodes.push({ id, name: company, column: 0, type: 'company' });
    nodeIndex.set(id, nodes.length - 1);
  });
  
  // Add year-targetLabel nodes (columns 1-5) - one node per unique targetLabel per year
  // For Market, also include relType in the node ID to distinguish Entering/Expanding/Exiting
  years.forEach((year, yearIdx) => {
    if (isMarketDimension) {
      // For Market: create separate nodes for each relType-targetLabel combination
      const relTypeMap = yearTargetLabels.has(year) ? yearTargetLabels.get(year) : new Map();
      relTypeMap.forEach((targetLabels, relType) => {
        const sortedLabels = Array.from(targetLabels).sort();
        sortedLabels.forEach(targetLabel => {
          const state = stateMap[relType];
          const id = `year-${year}-${relType}-${targetLabel}`;
          nodes.push({ 
            id, 
            name: targetLabel, 
            column: yearIdx + 1, 
            year, 
            targetLabel,
            relType,
            state, // Store the state (Entering, Expanding, Exiting)
            type: 'target'
          });
          nodeIndex.set(id, nodes.length - 1);
        });
      });
    } else {
      // For Risk/Opportunity: one node per targetLabel per year
      const targetLabels = yearTargetLabels.has(year) 
        ? Array.from(yearTargetLabels.get(year)).sort() 
        : [];
      
      targetLabels.forEach(targetLabel => {
        const id = `year-${year}-${targetLabel}`;
        nodes.push({ 
          id, 
          name: targetLabel, 
          column: yearIdx + 1, 
          year, 
          targetLabel,
          type: 'target'
        });
        nodeIndex.set(id, nodes.length - 1);
      });
    }
  });
  
  // Generate links: company -> year-targetLabel transitions
  const links = [];
  
  companies.forEach(company => {
    const yearMap = companyYearLabels.get(company);
    if (!yearMap) return;
    
    // Link from company to targetLabel nodes in ALL years where this company appears
    years.forEach(year => {
      const yearData = yearMap.get(year);
      if (!yearData) return;
      
      if (isMarketDimension) {
        // For Market: iterate through relType -> targetLabels
        yearData.forEach((targetLabels, relType) => {
          targetLabels.forEach(targetLabel => {
            const sourceId = `company-${company}`;
            const targetId = `year-${year}-${relType}-${targetLabel}`;
            if (nodeIndex.has(targetId)) {
              links.push({
                source: nodeIndex.get(sourceId),
                target: nodeIndex.get(targetId),
                value: 1,
                company,
                relType
              });
            }
          });
        });
      } else {
        // For Risk/Opportunity: iterate through targetLabels
        yearData.forEach(targetLabel => {
          const sourceId = `company-${company}`;
          const targetId = `year-${year}-${targetLabel}`;
          links.push({
            source: nodeIndex.get(sourceId),
            target: nodeIndex.get(targetId),
            value: 1,
            company
          });
        });
      }
    });
    
    // Link between ALL year pairs - ONLY connect same target.label across years
    // For Market: also require same relType (state)
    // This tracks continuity of the same target value across any years (not just consecutive)
    for (let i = 0; i < years.length; i++) {
      const fromYear = years[i];
      const fromData = yearMap.get(fromYear);
      
      // Connect to all future years (not just the next one)
      for (let j = i + 1; j < years.length; j++) {
        const toYear = years[j];
        const toData = yearMap.get(toYear);
        
        if (!fromData || !toData) continue;
        
        if (isMarketDimension) {
          // For Market: only connect same targetLabel AND same relType
          fromData.forEach((fromLabels, relType) => {
            const toLabels = toData.get(relType);
            if (!toLabels) return;
            
            // Find intersection: labels that appear in both years with the same relType
            const commonLabels = new Set();
            fromLabels.forEach(label => {
              if (toLabels.has(label)) {
                commonLabels.add(label);
              }
            });
            
            // Create links ONLY for the same target.label AND same relType
            commonLabels.forEach(targetLabel => {
              const sourceId = `year-${fromYear}-${relType}-${targetLabel}`;
              const targetId = `year-${toYear}-${relType}-${targetLabel}`;
              
              if (nodeIndex.has(sourceId) && nodeIndex.has(targetId)) {
                links.push({
                  source: nodeIndex.get(sourceId),
                  target: nodeIndex.get(targetId),
                  value: 1,
                  company,
                  fromLabel: targetLabel,
                  toLabel: targetLabel,
                  relType,
                  isContinuous: true, // Always true since we only link same labels and same state
                  continuityType: 'same'
                });
              }
            });
          });
        } else {
          // For Risk/Opportunity: only connect same targetLabel
          // Find intersection: labels that appear in both years
          const commonLabels = new Set();
          fromData.forEach(label => {
            if (toData.has(label)) {
              commonLabels.add(label);
            }
          });
          
          // Create links ONLY for the same target.label
          commonLabels.forEach(targetLabel => {
            const sourceId = `year-${fromYear}-${targetLabel}`;
            const targetId = `year-${toYear}-${targetLabel}`;
            
            links.push({
              source: nodeIndex.get(sourceId),
              target: nodeIndex.get(targetId),
              value: 1,
              company,
              fromLabel: targetLabel,
              toLabel: targetLabel,
              isContinuous: true, // Always true since we only link same labels
              continuityType: 'same'
            });
          });
        }
      }
    }
  });
  
  // Aggregate links with same source/target
  const linkMap = new Map();
  links.forEach(link => {
    const key = `${link.source}-${link.target}`;
    if (linkMap.has(key)) {
      const existing = linkMap.get(key);
      existing.value += link.value;
      // Preserve continuity if any link in the aggregation is continuous
      if (link.isContinuous) {
        existing.isContinuous = true;
        existing.continuityType = 'same';
        existing.fromLabel = link.fromLabel;
        existing.toLabel = link.toLabel;
      }
    } else {
      linkMap.set(key, { ...link });
    }
  });
  
  return {
    nodes: nodes.map((n, i) => ({ ...n, index: i })),
    links: Array.from(linkMap.values())
  };
})()
```

<!--{"pinCode":false,"dname":"83f4adb3-5ec0-45b6-9d7b-7265bf134b57","codeMode":"js","hide":true}-->
```js
loadingInfo = (async () => {
  // Show loading message while data is being fetched
  // This cell will show "Loading..." automatically while the promise is pending
  try {
    await data;
    await sankeyData;
    return "Ready"; // Show "Ready" when data is loaded (or return null to hide)
  } catch (error) {
    return `Error: ${error.message}`;
  }
})()
```

<!--{"pinCode":false,"dname":"9cd58c09-476a-47ab-865f-d8c5188413f6","codeMode":"js"}-->
```js
chart = (async () => {
  const width = 1200;
  const height = 800;
  const margin = { top: 20, right: 20, bottom: 20, left: 130 };
  
  // Show loading indicator while data is being fetched
  const { nodes, links } = await sankeyData;
  const dimension = selectedDimensionComboBox;
  
  if (!nodes || nodes.length === 0) {
    return d3.create("svg")
      .attr("width", width)
      .attr("height", height)
      .attr("style", "max-width: 100%; height: auto;")
      .append("text")
      .attr("x", width / 2)
      .attr("y", height / 2)
      .attr("text-anchor", "middle")
      .attr("font-size", "16px")
      .attr("fill", "#666")
      .text("No data available")
      .node();
  }
  
  // Debug: log node and link counts
  console.log(`Rendering chart with ${nodes.length} nodes and ${links.length} links`);
  console.log(`Company nodes: ${nodes.filter(n => n.type === 'company').length}`);
  console.log(`Target nodes: ${nodes.filter(n => n.type === 'target').length}`);
  
  // Debug: log column distribution
  const debugColumnGroups = d3.group(nodes, d => d.column);
  console.log('Column distribution:', Array.from(debugColumnGroups.entries()).map(([col, nodes]) => 
    `Column ${col}: ${nodes.length} nodes`
  ));
  
  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("width", width)
    .attr("height", height)
    .attr("style", "max-width: 100%; height: auto; overflow: visible;");
  
  // Create a container group for zoom/pan - all content will be added to this
  const container = svg.append("g")
    .attr("class", "zoom-container");
  
  // Color scales
  const companyColor = d3.scaleOrdinal(d3.schemeCategory10);
  
  // State colors based on dimension
  let stateColor;
  if (dimension === 'Risk') {
    stateColor = d3.scaleOrdinal()
      .domain(['Risk'])
      .range(['#ff6b6b']);
  } else if (dimension === 'Opportunity') {
    stateColor = d3.scaleOrdinal()
      .domain(['Opportunity'])
      .range(['#4ecdc4']);
  } else if (dimension === 'Market') {
    stateColor = d3.scaleOrdinal()
      .domain(['Entering', 'Expanding', 'Exiting'])
      .range(['#45b7d1', '#96ceb4', '#ffeaa7']);
  } else {
    stateColor = d3.scaleOrdinal()
      .domain(['Risk'])
      .range(['#ff6b6b']);
  }
  
  // Calculate flow values for each node (sum of incoming/outgoing links)
  nodes.forEach(node => {
    node.value = 0;
  });
  
  links.forEach(link => {
    const source = nodes[link.source];
    const target = nodes[link.target];
    if (source) source.value += link.value;
    if (target) target.value += link.value;
  });
  
  // Calculate value changes for continuous links (same target.label across years)
  links.forEach(link => {
    if (link.isContinuous) {
      const source = nodes[link.source];
      const target = nodes[link.target];
      if (source && target) {
        // Calculate value change: positive = increase, negative = decrease
        link.valueChange = target.value - source.value;
        link.valueChangePercent = source.value > 0 
          ? ((target.value - source.value) / source.value) * 100 
          : 0;
      }
    }
  });
  
  // Ensure all nodes have at least a minimum value for visibility
  nodes.forEach(node => {
    if (node.value === 0) {
      // Give nodes with no links a small value so they're still visible
      node.value = 0.5;
    }
  });
  
  // Years array for alignment
  const years = [2020, 2021, 2022, 2023, 2024];
  
  // Calculate column positions
  const numColumns = 6; // companies + 5 years
  const columnWidth = 150;
  const gapAfterCompanies = 50; // Extra space between Companies and first year
  const totalWidth = columnWidth * numColumns + gapAfterCompanies;
  const startX = margin.left; // Start from left margin instead of centering
  // Custom scale to add gap after column 0 (Companies)
  const columnX = (column) => {
    if (column === 0) {
      return startX;
    } else {
      // Add gap after companies column
      return startX + columnWidth + gapAfterCompanies + (column - 1) * columnWidth;
    }
  };
  
  // Group nodes by column and calculate vertical positions based on flow
  // Ensure all 6 columns exist (even if empty)
  const columnGroups = new Map();
  for (let i = 0; i < 6; i++) {
    columnGroups.set(i, []);
  }
  nodes.forEach(node => {
    if (!columnGroups.has(node.column)) {
      columnGroups.set(node.column, []);
    }
    columnGroups.get(node.column).push(node);
  });
  const nodePadding = 2;
  const minNodeHeight = 8;
  const maxNodeHeight = 40;
  
  // Calculate total flow per column for scaling
  const columnTotals = new Map();
  columnGroups.forEach((groupNodes, column) => {
    const total = d3.sum(groupNodes, d => d.value || 1);
    columnTotals.set(column, total);
  });
  
  // Calculate actual content height needed
  let maxContentHeight = 0;
  
  // Position nodes in each column
  columnGroups.forEach((groupNodes, column) => {
    const x = columnX(column);
    const columnTotal = columnTotals.get(column) || 1;
    // Use a large available height to fit all nodes
    const availableHeight = Math.max(height - margin.top - margin.bottom - 100, 2000);
    let y = margin.top + 80;
    
    // Sort nodes by value (descending) - bigger nodes on top
    const sortedNodes = Array.from(groupNodes).sort((a, b) => {
      const valueA = a.value || 0;
      const valueB = b.value || 0;
      return valueB - valueA; // Descending order (largest first)
    });
    const nodeCount = sortedNodes.length;
    
    // If we have nodes, distribute them evenly, but size based on value
    if (nodeCount > 0) {
      const minHeightPerNode = Math.max(minNodeHeight, (availableHeight / nodeCount) - nodePadding);
      
      sortedNodes.forEach((node, i) => {
        const nodeValue = node.value || 1;
        // Calculate height based on value, but ensure minimum visibility
        const proportionalHeight = columnTotal > 0 
          ? (nodeValue / columnTotal) * availableHeight 
          : minHeightPerNode;
        const nodeHeight = Math.max(
          minHeightPerNode,
          Math.min(maxNodeHeight, proportionalHeight)
        );
        
        node.x0 = x;
        node.x1 = x + columnWidth;
        node.y0 = y;
        node.y1 = y + nodeHeight;
        y += nodeHeight + nodePadding;
      });
      
      // Track maximum height needed
      if (y > maxContentHeight) {
        maxContentHeight = y;
      }
    }
  });
  
  // Calculate actual height needed (add bottom margin)
  const actualHeight = Math.max(height, maxContentHeight + margin.bottom + 50);
  
  // Helper function to create curved path for Sankey links
  // Handles both rectangular (company) and circular (target) nodes
  const sankeyPath = (source, target, value, customWidth = null) => {
    // Calculate connection points based on node type
    let x0, y0, x1, y1;
    
    // Source node connection point
    if (source.type === 'company') {
      // Rectangle: connect from right edge
      x0 = source.x1;
      y0 = (source.y0 + source.y1) / 2;
    } else {
      // Circle: connect from right edge of circle
      const r = Math.min((source.x1 - source.x0) / 2, (source.y1 - source.y0) / 2);
      const centerX = (source.x0 + source.x1) / 2;
      const centerY = (source.y0 + source.y1) / 2;
      // Calculate point on circle at angle 0 (right side)
      x0 = centerX + r;
      y0 = centerY;
    }
    
    // Target node connection point
    if (target.type === 'company') {
      // Rectangle: connect to left edge
      x1 = target.x0;
      y1 = (target.y0 + target.y1) / 2;
    } else {
      // Circle: connect to left edge of circle
      const r = Math.min((target.x1 - target.x0) / 2, (target.y1 - target.y0) / 2);
      const centerX = (target.x0 + target.x1) / 2;
      const centerY = (target.y0 + target.y1) / 2;
      // Calculate point on circle at angle π (left side)
      x1 = centerX - r;
      y1 = centerY;
    }
    
    // Make links more visible - scale width based on value, or use custom width
    const width = customWidth !== null ? customWidth : Math.max(3, Math.sqrt(value) * 5);
    
    const dx = x1 - x0;
    const curvature = 0.3;
    
    // Create a ribbon-like path (wider at center)
    const path = d3.path();
    path.moveTo(x0, y0 - width / 2);
    path.bezierCurveTo(
      x0 + dx * curvature, y0 - width / 2,
      x1 - dx * curvature, y1 - width / 2,
      x1, y1 - width / 2
    );
    path.lineTo(x1, y1 + width / 2);
    path.bezierCurveTo(
      x1 - dx * curvature, y1 + width / 2,
      x0 + dx * curvature, y0 + width / 2,
      x0, y0 + width / 2
    );
    path.closePath();
    
    return path.toString();
  };
  
  // Draw links (ribbons) - draw first so they appear behind nodes
  // Show all links - from companies to targets and between targets
  // Store original link index in the data for proper filtering
  const filteredLinksData = links
    .map((d, i) => ({ ...d, originalIndex: i }))
    .filter(d => {
      const source = nodes[d.source];
      const target = nodes[d.target];
      // Only show links where both nodes exist and have valid positions
      return source && target && source.x0 !== undefined && target.x0 !== undefined;
    });
  
  const link = container.append("g")
    .attr("class", "links")
    .selectAll("path")
    .data(filteredLinksData)
    .join("path")
      .attr("d", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        if (!source || !target) return "M 0 0";
        // Make continuous links wider
        const baseWidth = Math.max(3, Math.sqrt(d.value) * 5);
        const width = d.isContinuous ? baseWidth * 1.5 : baseWidth;
        return sankeyPath(source, target, d.value, width);
      })
      .attr("fill", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        // Use company color for links originating from companies
        if (source.type === 'company') {
          return companyColor(source.name);
        }
        // For Market dimension, use state-specific colors
        if (dimension === 'Market' && target && target.state) {
          // For continuous links, use a brighter/highlighted color
          if (d.isContinuous) {
            // Color based on value change: green for increase, orange for decrease, state color for stable
            if (d.valueChange > 0) {
              return '#4ade80'; // Green for increase
            } else if (d.valueChange < 0) {
              return '#fb923c'; // Orange for decrease
            } else {
              return stateColor(target.state); // State color for stable
            }
          }
          // For target-to-target links, use state color
          return stateColor(target.state);
        }
        // For continuous links, use a brighter/highlighted color
        if (d.isContinuous) {
          // Color based on value change: green for increase, orange for decrease, blue for stable
          if (d.valueChange > 0) {
            return '#4ade80'; // Green for increase
          } else if (d.valueChange < 0) {
            return '#fb923c'; // Orange for decrease
          } else {
            return dimension === 'Risk' ? '#ff6b6b' : 
                   dimension === 'Opportunity' ? '#4ecdc4' : 
                   '#45b7d1'; // Default dimension color for stable
          }
        }
        // For target-to-target links, use dimension color
        return dimension === 'Risk' ? '#ff6b6b' : 
               dimension === 'Opportunity' ? '#4ecdc4' : 
               '#45b7d1';
      })
      .attr("fill-opacity", d => d.isContinuous ? 0.9 : 0.7) // Brighter for continuous
      .attr("stroke", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        if (source.type === 'company') {
          return companyColor(source.name);
        }
        // For Market dimension, use state-specific colors
        if (dimension === 'Market' && target && target.state) {
          // Continuous links get brighter stroke
          if (d.isContinuous) {
            if (d.valueChange > 0) {
              return '#22c55e'; // Darker green for increase
            } else if (d.valueChange < 0) {
              return '#f97316'; // Darker orange for decrease
            } else {
              // Use a darker shade of the state color for stable
              const stateColors = {
                'Entering': '#2ea3d1',
                'Expanding': '#7ab89a',
                'Exiting': '#ffd966'
              };
              return stateColors[target.state] || stateColor(target.state);
            }
          }
          // For target-to-target links, use darker state color
          const stateColors = {
            'Entering': '#2ea3d1',
            'Expanding': '#7ab89a',
            'Exiting': '#ffd966'
          };
          return stateColors[target.state] || stateColor(target.state);
        }
        // Continuous links get brighter stroke
        if (d.isContinuous) {
          if (d.valueChange > 0) {
            return '#22c55e'; // Darker green for increase
          } else if (d.valueChange < 0) {
            return '#f97316'; // Darker orange for decrease
          } else {
            return dimension === 'Risk' ? '#ff4444' : 
                   dimension === 'Opportunity' ? '#2eb8b3' : 
                   '#2ea3d1'; // Brighter dimension color for stable
          }
        }
        return dimension === 'Risk' ? '#ff6b6b' : 
               dimension === 'Opportunity' ? '#4ecdc4' : 
               '#45b7d1';
      })
      .attr("stroke-width", d => {
        const baseWidth = Math.max(2, Math.min(4, Math.sqrt(d.value) * 2));
        return d.isContinuous ? baseWidth * 1.5 : baseWidth; // Thicker for continuous
      })
      .attr("stroke-opacity", d => d.isContinuous ? 1.0 : 0.9);
  
  // Draw nodes - draw after links so they appear on top
  // Make nodes more transparent so flows are more visible
  // Use ellipses for target nodes, rectangles for company nodes
  const node = container.append("g")
    .selectAll("g")
    .data(nodes)
    .join("g")
      .style("cursor", "pointer"); // Add pointer cursor for interactivity
  
  // Draw rectangles for company nodes
  node.filter(d => d.type === 'company')
    .append("rect")
      .attr("x", d => d.x0)
      .attr("y", d => d.y0)
      .attr("width", d => d.x1 - d.x0)
      .attr("height", d => d.y1 - d.y0)
      .attr("fill", d => companyColor(d.name))
      .attr("fill-opacity", 0.4)
      .attr("stroke", "#000")
      .attr("stroke-width", 1)
      .attr("stroke-opacity", 0.6);
  
  // Draw circles for target nodes
  node.filter(d => d.type === 'target')
    .append("circle")
      .attr("cx", d => (d.x0 + d.x1) / 2)
      .attr("cy", d => (d.y0 + d.y1) / 2)
      .attr("r", d => Math.min((d.x1 - d.x0) / 2, (d.y1 - d.y0) / 2)) // Use smaller dimension for perfect circle
      .attr("fill", d => {
        // For Market dimension, use state-specific colors
        if (dimension === 'Market' && d.state) {
          return stateColor(d.state);
        }
        // For other dimensions, use dimension color
        return dimension === 'Risk' ? '#ff6b6b' : 
               dimension === 'Opportunity' ? '#4ecdc4' : 
               '#45b7d1';
      })
      .attr("fill-opacity", 0.4)
      .attr("stroke", "#000")
      .attr("stroke-width", 1)
      .attr("stroke-opacity", 0.6);
  
  // Add labels for nodes
  const labels = container.append("g")
    .attr("class", "node-labels")
    .selectAll("g")
    .data(nodes)
    .join("g");
  
  // Add text for company nodes - centered with vertical padding
  // Make text hoverable by ensuring it's part of the hoverable group
  labels.filter(d => d.type === 'company')
    .append("text")
      .attr("x", d => d.x0 + (d.x1 - d.x0) / 2) // Center in the node
      .attr("y", d => {
        const verticalPadding = 3; // Vertical padding in pixels
        return d.y0 + verticalPadding + (d.y1 - d.y0 - verticalPadding * 2) / 2; // Center with padding
      })
      .attr("dy", "0.35em")
      .attr("text-anchor", "middle") // Center-align text
      .attr("font-size", "10px")
      .attr("fill", "#000")
      .style("pointer-events", "auto") // Make text hoverable
      .style("cursor", "pointer")
      .text(d => d.name);
  
  // Helper function to wrap text into multiple lines
  const wrapText = (text, maxWidth, fontSize = 11) => {
    const words = text.split(/\s+/);
    const lines = [];
    let currentLine = words[0];
    
    // Approximate character width (fontSize * 0.6 is a rough estimate)
    const charWidth = fontSize * 0.6;
    const maxCharsPerLine = Math.floor(maxWidth / charWidth);
    
    for (let i = 1; i < words.length; i++) {
      const word = words[i];
      const testLine = currentLine + ' ' + word;
      if (testLine.length <= maxCharsPerLine) {
        currentLine = testLine;
      } else {
        lines.push(currentLine);
        currentLine = word;
      }
    }
    lines.push(currentLine);
    return lines;
  };
  
  // Add labels for target nodes - show target.label centered inside the elliptical node
  const targetLabels = labels.filter(d => d.type === 'target');
  
  targetLabels.each(function(d) {
    const targetLabel = d.name || d.targetLabel || '';
    const nodeWidth = d.x1 - d.x0;
    const nodeHeight = d.y1 - d.y0;
    const nodeCenterX = (d.x0 + d.x1) / 2;
    const nodeCenterY = (d.y0 + d.y1) / 2;
    const value = Math.round(d.value || 0);
    
    if (targetLabel) {
      const g = d3.select(this);
      
      // Calculate available width for text (use 80% of node width for padding)
      const maxLabelWidth = nodeWidth * 0.8;
      const fontSize = Math.max(8, Math.min(11, nodeHeight / 6)); // Scale font size with node height
      const lineHeight = fontSize * 1.2;
      const wrappedLines = wrapText(targetLabel, maxLabelWidth, fontSize);
      const totalTextHeight = wrappedLines.length * lineHeight;
      
      // Calculate starting Y position to center the text vertically
      const startY = nodeCenterY - (totalTextHeight / 2) + (lineHeight / 2);
      
      // Create text element centered in the node
      const textElement = g.append("text")
        .attr("x", nodeCenterX)
        .attr("y", startY)
        .attr("text-anchor", "middle")
        .attr("font-size", `${fontSize}px`)
        .attr("font-weight", "normal")
        .attr("fill", "#000")
        .style("pointer-events", "auto") // Make text hoverable
        .style("cursor", "pointer");
      
      // Add each line as a tspan, centered
      wrappedLines.forEach((line, i) => {
        textElement.append("tspan")
          .attr("x", nodeCenterX)
          .attr("dy", i === 0 ? 0 : lineHeight)
          .text(line);
      });
      
      // Show value below the label if there's space
      if (nodeHeight > 30 && value > 0) {
        const valueY = nodeCenterY + (totalTextHeight / 2) + 12;
        g.append("text")
          .attr("x", nodeCenterX)
          .attr("y", valueY)
          .attr("dy", "0.35em")
          .attr("text-anchor", "middle")
          .attr("font-size", `${Math.max(10, Math.min(14, nodeHeight / 4))}px`)
          .attr("font-weight", "bold")
          .attr("fill", "#fff")
          .attr("stroke", "#000")
          .attr("stroke-width", "0.5px")
          .attr("paint-order", "stroke")
          .style("pointer-events", "auto") // Make value text hoverable
          .style("cursor", "pointer")
          .text(value.toString());
      }
    }
  });
  
  // Add value labels to links (show in the middle of significant links)
  // Create a map to track which link index each label corresponds to
  const linkLabelMap = new Map();
  const filteredLinks = links.filter((d, i) => {
    const source = nodes[d.source];
    const target = nodes[d.target];
    return source && target && (d.isContinuous || d.value > 1);
  });
  
  filteredLinks.forEach((link, filteredIdx) => {
    const originalIdx = links.indexOf(link);
    linkLabelMap.set(filteredIdx, originalIdx);
  });
  
  const linkLabels = container.append("g")
    .attr("class", "link-labels")
    .selectAll("text")
    .data(filteredLinks.map((d, i) => ({ ...d, linkIndex: linkLabelMap.get(i) })))
    .join("text")
      .attr("x", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        return (source.x1 + target.x0) / 2;
      })
      .attr("y", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        return (source.y0 + source.y1 + target.y0 + target.y1) / 4;
      })
      .attr("text-anchor", "middle")
      .attr("font-size", d => d.isContinuous ? "10px" : "9px")
      .attr("fill", "#fff")
      .attr("font-weight", d => d.isContinuous ? "bold" : "normal")
      .attr("pointer-events", "none")
      .attr("stroke", "#000")
      .attr("stroke-width", "0.5px")
      .attr("stroke-opacity", 0.8)
      .attr("paint-order", "stroke")
      .text(d => {
        if (d.isContinuous && d.valueChange !== undefined) {
          // Show value change for continuous links
          const change = Math.round(d.valueChange);
          const percent = Math.round(d.valueChangePercent);
          if (change > 0) {
            return `+${change} (+${percent}%)`;
          } else if (change < 0) {
            return `${change} (${percent}%)`;
          } else {
            return `${Math.round(d.value)} (stable)`;
          }
        }
        return Math.round(d.value);
      });
  
  // Create tooltip
  const tooltip = d3.select("body").append("div")
    .attr("class", "sankey-tooltip")
    .style("position", "absolute")
    .style("padding", "8px 12px")
    .style("background", "rgba(0, 0, 0, 0.85)")
    .style("color", "#fff")
    .style("border-radius", "4px")
    .style("font-size", "12px")
    .style("pointer-events", "none")
    .style("opacity", 0)
    .style("z-index", 1000)
    .style("box-shadow", "0 2px 8px rgba(0,0,0,0.3)");
  
  // Helper function to get related node indices
  const getRelatedNodeIndices = (nodeIndex) => {
    const related = new Set([nodeIndex]);
    links.forEach(link => {
      if (link.source === nodeIndex || link.target === nodeIndex) {
        related.add(link.source);
        related.add(link.target);
      }
    });
    return related;
  };
  
  // Helper function to get related link indices
  const getRelatedLinkIndices = (nodeIndex) => {
    const related = new Set();
    links.forEach((link, i) => {
      if (link.source === nodeIndex || link.target === nodeIndex) {
        related.add(i);
      }
    });
    return related;
  };
  
  // Track selected nodes (persistent selection)
  const selectedNodes = new Set();
  
  // Track isolated node (showing only that node and its connections)
  let isolatedNodeIndex = null;
  
  // Store original node positions for restoration
  const originalNodePositions = new Map();
  nodes.forEach(n => {
    originalNodePositions.set(n.index, {
      x0: n.x0,
      x1: n.x1,
      y0: n.y0,
      y1: n.y1
    });
  });
  
  // Store original node values and link value changes for restoration
  const originalNodeValues = new Map();
  nodes.forEach(n => {
    originalNodeValues.set(n.index, n.value);
  });
  
  const originalLinkValueChanges = new Map();
  links.forEach((link, i) => {
    originalLinkValueChanges.set(i, {
      valueChange: link.valueChange,
      valueChangePercent: link.valueChangePercent
    });
  });
  
  // Function to recalculate node values and link value changes based on visible links
  const recalculateValues = function() {
    if (isolatedNodeIndex === null) {
      // If not isolated, restore original values
      nodes.forEach(n => {
        const originalValue = originalNodeValues.get(n.index);
        if (originalValue !== undefined) {
          n.value = originalValue;
        }
      });
      
      links.forEach((link, i) => {
        const original = originalLinkValueChanges.get(i);
        if (original) {
          link.valueChange = original.valueChange;
          link.valueChangePercent = original.valueChangePercent;
        }
      });
    } else {
      // If isolated, recalculate based only on visible links
      const relatedNodes = getRelatedNodeIndices(isolatedNodeIndex);
      const relatedLinks = getRelatedLinkIndices(isolatedNodeIndex);
      
      // Reset node values
      nodes.forEach(n => {
        n.value = 0;
      });
      
      // Recalculate node values from only visible links
      links.forEach((link, i) => {
        if (relatedLinks.has(i)) {
          const source = nodes[link.source];
          const target = nodes[link.target];
          if (source && target) {
            source.value += link.value;
            target.value += link.value;
          }
        }
      });
      
      // Recalculate value changes for continuous links (only visible ones)
      links.forEach((link, i) => {
        if (link.isContinuous && relatedLinks.has(i)) {
          const source = nodes[link.source];
          const target = nodes[link.target];
          if (source && target) {
            link.valueChange = target.value - source.value;
            link.valueChangePercent = source.value > 0 
              ? ((target.value - source.value) / source.value) * 100 
              : 0;
          }
        }
      });
    }
    
    // Update link label text with new values
    // Only update labels for visible links (related to isolated node if isolated)
    linkLabels
      .transition()
      .duration(200)
      .text(function(d) {
        // If isolated, only update text for related links
        if (isolatedNodeIndex !== null) {
          const linkIdx = d.linkIndex !== undefined ? d.linkIndex : -1;
          const relatedLinks = getRelatedLinkIndices(isolatedNodeIndex);
          if (!relatedLinks.has(linkIdx)) {
            // Don't update text for non-related links when isolated
            return d3.select(this).text(); // Keep existing text
          }
        }
        
        // Get the current link data from the links array using linkIndex
        const linkIdx = d.linkIndex !== undefined ? d.linkIndex : -1;
        const currentLink = linkIdx >= 0 && linkIdx < links.length ? links[linkIdx] : d;
        
        if (currentLink.isContinuous && currentLink.valueChange !== undefined) {
          // Show value change for continuous links
          const change = Math.round(currentLink.valueChange);
          const percent = Math.round(currentLink.valueChangePercent);
          if (change > 0) {
            return `+${change} (+${percent}%)`;
          } else if (change < 0) {
            return `${change} (${percent}%)`;
          } else {
            return `${Math.round(currentLink.value)} (stable)`;
          }
        }
        return Math.round(currentLink.value);
      });
  };
  
  // Function to update visual state based on isolated/selected nodes
  const updateIsolation = function() {
    // Recalculate values based on visible links
    recalculateValues();
    
    if (isolatedNodeIndex === null) {
      // Show all nodes - reset everything to default state
      node
        .transition()
        .duration(200)
        .attr("opacity", 1);
      
      // Reset node shapes
      node
        .selectAll("rect, circle")
        .transition()
        .duration(200)
        .attr("fill-opacity", 0.4)
        .attr("stroke", "#000") // Reset to default black stroke
        .attr("stroke-opacity", 0.6)
        .attr("stroke-width", 1);
      
      link
        .transition()
        .duration(200)
        .attr("fill-opacity", d => d.isContinuous ? 0.9 : 0.7)
        .attr("stroke-opacity", d => d.isContinuous ? 1.0 : 0.9)
        .attr("stroke-width", d => {
          const baseWidth = Math.max(2, Math.min(4, Math.sqrt(d.value) * 2));
          return d.isContinuous ? baseWidth * 1.5 : baseWidth;
        });
      
      labels
        .transition()
        .duration(200)
        .attr("opacity", 1);
      
      // Restore all link labels (show all diff numbers)
      linkLabels
        .transition()
        .duration(200)
        .attr("opacity", 1)
        .text(function(d) {
          // Restore original text values when un-isolating
          const linkIdx = d.linkIndex !== undefined ? d.linkIndex : -1;
          const currentLink = linkIdx >= 0 && linkIdx < links.length ? links[linkIdx] : d;
          
          if (currentLink.isContinuous && currentLink.valueChange !== undefined) {
            const change = Math.round(currentLink.valueChange);
            const percent = Math.round(currentLink.valueChangePercent);
            if (change > 0) {
              return `+${change} (+${percent}%)`;
            } else if (change < 0) {
              return `${change} (${percent}%)`;
            } else {
              return `${Math.round(currentLink.value)} (stable)`;
            }
          }
          return Math.round(currentLink.value);
        });
      
      // Then update selection state
      updateSelection();
      return;
    }
    
    // Get all related nodes and links for isolated node
    const relatedNodes = getRelatedNodeIndices(isolatedNodeIndex);
    const relatedLinks = getRelatedLinkIndices(isolatedNodeIndex);
    
    // Hide non-related nodes and links - set opacity on node groups first
    node
      .transition()
      .duration(200)
      .attr("opacity", function(d) {
        const isVisible = relatedNodes.has(d.index);
        return isVisible ? 1 : 0;
      });
    
    // Update node shapes for visible nodes only
    node
      .selectAll("rect, circle")
      .transition()
      .duration(200)
      .attr("fill-opacity", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) return 0;
        return relatedNodes.has(parentNode.index) ? 0.9 : 0;
      })
      .attr("stroke", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) return "#000";
        // Make stroke red for isolated/selected nodes
        return relatedNodes.has(parentNode.index) ? "#ff0000" : "#000";
      })
      .attr("stroke-opacity", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) return 0;
        return relatedNodes.has(parentNode.index) ? 1.0 : 0;
      })
      .attr("stroke-width", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) return 0;
        return relatedNodes.has(parentNode.index) ? 3 : 0; // Thicker stroke for selected nodes
      });
    
    // Hide non-related links
    link
      .transition()
      .duration(200)
      .attr("fill-opacity", (d) => relatedLinks.has(d.originalIndex) ? 0.95 : 0)
      .attr("stroke-opacity", (d) => relatedLinks.has(d.originalIndex) ? 1.0 : 0)
      .attr("stroke-width", (d) => relatedLinks.has(d.originalIndex) ? 
        (d.isContinuous ? Math.max(3, Math.min(6, Math.sqrt(d.value) * 3)) : Math.max(2, Math.min(5, Math.sqrt(d.value) * 2.5))) : 0);
    
    // Hide non-related labels
    labels
      .transition()
      .duration(200)
      .attr("opacity", function(d) {
        return relatedNodes.has(d.index) ? 1 : 0;
      });
    
    // Hide non-related link labels (completely hide diff numbers for non-selected nodes)
    linkLabels
      .transition()
      .duration(200)
      .attr("opacity", (labelD) => {
        const linkIdx = labelD.linkIndex !== undefined ? labelD.linkIndex : -1;
        // Only show diff numbers for links related to the isolated node
        return relatedLinks.has(linkIdx) ? 1 : 0;
      });
  };
  
  // Function to update visual state based on selected nodes
  const updateSelection = function() {
    // Get all related nodes and links for selected nodes
    const relatedNodes = new Set();
    const relatedLinks = new Set();
    
    selectedNodes.forEach(nodeIndex => {
      relatedNodes.add(nodeIndex);
      const related = getRelatedNodeIndices(nodeIndex);
      related.forEach(idx => relatedNodes.add(idx));
      
      const linkIndices = getRelatedLinkIndices(nodeIndex);
      linkIndices.forEach(idx => relatedLinks.add(idx));
    });
    
    // Update link opacity and stroke
      link
        .transition()
        .duration(200)
        .attr("fill-opacity", (d) => {
          if (selectedNodes.size === 0) {
            return d.isContinuous ? 0.9 : 0.7;
          }
          return relatedLinks.has(d.originalIndex) ? 0.95 : 0.1;
        })
        .attr("stroke-opacity", (d) => {
          if (selectedNodes.size === 0) {
            return d.isContinuous ? 1.0 : 0.9;
          }
          return relatedLinks.has(d.originalIndex) ? 1.0 : 0.1;
        })
        .attr("stroke-width", (d) => {
          if (selectedNodes.size === 0) {
            const baseWidth = Math.max(2, Math.min(4, Math.sqrt(d.value) * 2));
            return d.isContinuous ? baseWidth * 1.5 : baseWidth;
          }
          return relatedLinks.has(d.originalIndex) ? 
            (d.isContinuous ? Math.max(3, Math.min(6, Math.sqrt(d.value) * 3)) : Math.max(2, Math.min(5, Math.sqrt(d.value) * 2.5))) : 
            (d.isContinuous ? Math.max(1, Math.min(2, Math.sqrt(d.value) * 1)) : Math.max(1, Math.min(1.5, Math.sqrt(d.value) * 1)));
        });
    
    // Update node opacity and stroke
    node
      .selectAll("rect, circle")
      .transition()
      .duration(200)
      .attr("fill-opacity", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) {
          return selectedNodes.size === 0 ? 0.4 : 0.1;
        }
        if (selectedNodes.size === 0) {
          return 0.4;
        }
        return relatedNodes.has(parentNode.index) ? 0.9 : 0.1;
      })
      .attr("stroke-opacity", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) {
          return 0.6;
        }
        const isCircle = this.tagName === 'circle';
        // Keep circles' stroke more visible even when dimmed
        if (selectedNodes.size === 0) {
          return 0.6;
        }
        if (isCircle) {
          return relatedNodes.has(parentNode.index) ? 1.0 : 0.6;
        }
        return relatedNodes.has(parentNode.index) ? 1.0 : 0.2;
      })
      .attr("stroke-width", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.index === undefined) {
          return 1;
        }
        if (selectedNodes.size === 0) {
          return 1;
        }
        return relatedNodes.has(parentNode.index) ? 2 : 1;
      });
    
    // Update link labels
    linkLabels
      .transition()
      .duration(200)
      .attr("opacity", (labelD) => {
        if (selectedNodes.size === 0) {
          return 1;
        }
        const linkIdx = labelD.linkIndex !== undefined ? labelD.linkIndex : -1;
        return relatedLinks.has(linkIdx) ? 1 : 0.2;
      });
  };
  
  // Shared hover handler function (temporary highlight on hover)
  const handleNodeHover = function(event, d) {
    const nodeIndex = d.index;
    const relatedNodes = getRelatedNodeIndices(nodeIndex);
    const relatedLinks = getRelatedLinkIndices(nodeIndex);
    
    // Only show temporary highlight if no nodes are isolated or selected
    if (isolatedNodeIndex !== null || selectedNodes.size > 0) {
      // If there's a selection, just show tooltip
      let tooltipText = "";
      if (d.type === 'company') {
        tooltipText = `<strong>${d.name}</strong><br/>Company`;
        const connections = links.filter(l => {
          const source = nodes[l.source];
          return source && source.name === d.name;
        }).length;
        if (connections > 0) {
          tooltipText += `<br/>${connections} connection${connections > 1 ? 's' : ''}`;
        }
      } else if (d.type === 'target') {
        tooltipText = `<strong>${d.name || d.targetLabel}</strong><br/>${dimension}`;
        // For Market dimension, show the state (Entering, Expanding, Exiting)
        if (dimension === 'Market' && d.state) {
          tooltipText += ` - ${d.state}`;
        }
        if (d.year) {
          tooltipText += `<br/>Year: ${d.year}`;
        }
        if (d.value) {
          tooltipText += `<br/>Value: ${Math.round(d.value)}`;
        }
      }
      tooltip
        .html(tooltipText)
        .style("opacity", 1)
        .style("left", (event.pageX + 10) + "px")
        .style("top", (event.pageY - 10) + "px");
      return;
    }
    
    // Hide non-related nodes and links on hover (only when nothing is selected)
    // Keep hovered node and its connections visible, hide everything else
    link
      .transition()
      .duration(200)
      .attr("fill-opacity", (d) => relatedLinks.has(d.originalIndex) ? 0.95 : 0)
      .attr("stroke-opacity", (d) => relatedLinks.has(d.originalIndex) ? 1.0 : 0)
      .attr("stroke-width", (d) => relatedLinks.has(d.originalIndex) ? 
        (d.isContinuous ? Math.max(3, Math.min(6, Math.sqrt(d.value) * 3)) : Math.max(2, Math.min(5, Math.sqrt(d.value) * 2.5))) : 0);
    
    // Hide non-related nodes (keep hovered node and its connections visible)
    // Ensure hovered node and its connections are always visible
    node
      .transition()
      .duration(200)
      .attr("opacity", function(d) {
        if (!d || d.index === undefined) return 0;
        // Always show hovered node itself and its connections, hide everything else
        // Explicitly check if this is the hovered node or a connected node
        const isHoveredNode = d.index === nodeIndex;
        const isConnected = relatedNodes.has(d.index);
        return (isHoveredNode || isConnected) ? 1 : 0;
      });
    
    // Update shapes for related nodes (keep them visible)
    node
      .filter(function(d) {
        if (!d || d.index === undefined) return false;
        const isHoveredNode = d.index === nodeIndex;
        const isConnected = relatedNodes.has(d.index);
        return isHoveredNode || isConnected;
      })
      .selectAll("rect, circle")
      .transition()
      .duration(200)
      .attr("fill-opacity", 0.9)
      .attr("stroke-opacity", 1.0)
      .attr("stroke-width", 2);
    
    // Hide shapes for non-related nodes
    node
      .filter(function(d) {
        if (!d || d.index === undefined) return false;
        const isHoveredNode = d.index === nodeIndex;
        const isConnected = relatedNodes.has(d.index);
        return !(isHoveredNode || isConnected);
      })
      .selectAll("rect, circle")
      .transition()
      .duration(200)
      .attr("fill-opacity", 0)
      .attr("stroke-opacity", 0)
      .attr("stroke-width", 0);
    
    // Hide labels for non-related nodes (keep hovered node and its connections visible)
    labels
      .transition()
      .duration(200)
      .attr("opacity", function(d) {
        if (!d || d.index === undefined) return 0;
        // Explicitly check if this is the hovered node or a connected node
        const isHoveredNode = d.index === nodeIndex;
        const isConnected = relatedNodes.has(d.index);
        return (isHoveredNode || isConnected) ? 1 : 0;
      });
    
    // Hide link labels for non-related links (keep hovered node's connections visible)
    linkLabels
      .transition()
      .duration(200)
      .attr("opacity", (labelD) => {
        const linkIdx = labelD.linkIndex !== undefined ? labelD.linkIndex : -1;
        // Show labels for links connected to hovered node, hide others
        return relatedLinks.has(linkIdx) ? 1 : 0;
      });
    
    // Show tooltip
    let tooltipText = "";
    if (d.type === 'company') {
      tooltipText = `<strong>${d.name}</strong><br/>Company`;
      const connections = links.filter(l => {
        const source = nodes[l.source];
        return source && source.name === d.name;
      }).length;
      if (connections > 0) {
        tooltipText += `<br/>${connections} connection${connections > 1 ? 's' : ''}`;
      }
    } else if (d.type === 'target') {
      tooltipText = `<strong>${d.name || d.targetLabel}</strong><br/>${dimension}`;
      // For Market dimension, show the state (Entering, Expanding, Exiting)
      if (dimension === 'Market' && d.state) {
        tooltipText += ` - ${d.state}`;
      }
      if (d.year) {
        tooltipText += `<br/>Year: ${d.year}`;
      }
      if (d.value) {
        tooltipText += `<br/>Value: ${Math.round(d.value)}`;
      }
    }
    
    tooltip
      .html(tooltipText)
      .style("opacity", 1)
      .style("left", (event.pageX + 10) + "px")
      .style("top", (event.pageY - 10) + "px");
  };
  
  const handleNodeMouseout = function() {
    // Only reset if nothing is isolated or selected
    if (isolatedNodeIndex === null && selectedNodes.size === 0) {
      updateIsolation(); // This will reset to default state
    }
    tooltip.style("opacity", 0);
  };
  
  // Function to redistribute visible nodes to fit the screen
  const redistributeNodes = function() {
    if (isolatedNodeIndex === null) {
      // If not isolated, restore original positions
      nodes.forEach(n => {
        const original = originalNodePositions.get(n.index);
        if (original) {
          n.x0 = original.x0;
          n.x1 = original.x1;
          n.y0 = original.y0;
          n.y1 = original.y1;
        }
      });
      
      // Reset zoom
      svg.transition()
        .duration(750)
        .call(zoom.transform, d3.zoomIdentity);
      
      // Update visual elements to original positions
      updateNodePositions();
      return;
    }
    
    // Get all related nodes (isolated node + its connections)
    const relatedNodes = getRelatedNodeIndices(isolatedNodeIndex);
    const visibleNodes = nodes.filter(n => relatedNodes.has(n.index));
    
    // Group visible nodes by column
    const visibleByColumn = new Map();
    visibleNodes.forEach(n => {
      if (!visibleByColumn.has(n.column)) {
        visibleByColumn.set(n.column, []);
      }
      visibleByColumn.get(n.column).push(n);
    });
    
    // Redistribute nodes within each column
    const availableHeight = height - margin.top - margin.bottom - 100;
    const startY = margin.top + 80;
    
    visibleByColumn.forEach((columnNodes, column) => {
      const x = columnX(column);
      const nodeCount = columnNodes.length;
      
      if (nodeCount > 0) {
        // Sort nodes by value (descending) - bigger nodes on top
        const sortedNodes = columnNodes.sort((a, b) => {
          const valueA = a.value || 0;
          const valueB = b.value || 0;
          return valueB - valueA; // Descending order (largest first)
        });
        
        // Calculate uniform height for all nodes in this column
        const uniformHeight = Math.max(minNodeHeight, Math.min(maxNodeHeight, (availableHeight / nodeCount) - nodePadding));
        let y = startY;
        
        sortedNodes.forEach((node) => {
          const nodeHeight = uniformHeight;
          
          // Update node position
          node.x0 = x;
          node.x1 = x + columnWidth;
          node.y0 = y;
          node.y1 = y + nodeHeight;
          y += nodeHeight + nodePadding;
        });
      }
    });
    
    // Update visual elements to new positions
    updateNodePositions();
  };
  
  // Helper function to update all visual elements based on node positions
  const updateNodePositions = function() {
    // Update node shapes (rect/circle) positions
    node
      .selectAll("rect")
      .transition()
      .duration(750)
      .attr("x", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.x0 === undefined) return 0;
        return parentNode.x0;
      })
      .attr("y", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.y0 === undefined) return 0;
        return parentNode.y0;
      })
      .attr("width", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.x0 === undefined || parentNode.x1 === undefined) return 0;
        return parentNode.x1 - parentNode.x0;
      })
      .attr("height", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.y0 === undefined || parentNode.y1 === undefined) return 0;
        return parentNode.y1 - parentNode.y0;
      });
    
    node
      .selectAll("circle")
      .transition()
      .duration(750)
      .attr("cx", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.x0 === undefined || parentNode.x1 === undefined) return 0;
        return (parentNode.x0 + parentNode.x1) / 2;
      })
      .attr("cy", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.y0 === undefined || parentNode.y1 === undefined) return 0;
        return (parentNode.y0 + parentNode.y1) / 2;
      })
      .attr("r", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.x0 === undefined || parentNode.x1 === undefined || parentNode.y0 === undefined || parentNode.y1 === undefined) return 0;
        return Math.min((parentNode.x1 - parentNode.x0) / 2, (parentNode.y1 - parentNode.y0) / 2);
      });
    
    // Update link paths to match new node positions
    link
      .transition()
      .duration(750)
      .attr("d", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        if (!source || !target) return "M 0 0";
        const baseWidth = Math.max(3, Math.sqrt(d.value) * 5);
        const linkWidth = d.isContinuous ? baseWidth * 1.5 : baseWidth;
        return sankeyPath(source, target, d.value, linkWidth);
      });
    
    // Update company label positions
    labels
      .filter(d => d.type === 'company')
      .selectAll("text")
      .transition()
      .duration(750)
      .attr("x", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.x0 === undefined || parentNode.x1 === undefined) return 0;
        return parentNode.x0 + (parentNode.x1 - parentNode.x0) / 2;
      })
      .attr("y", function() {
        const parentNode = d3.select(this.parentNode).datum();
        if (!parentNode || parentNode.y0 === undefined || parentNode.y1 === undefined) return 0;
        const verticalPadding = 3;
        return parentNode.y0 + verticalPadding + (parentNode.y1 - parentNode.y0 - verticalPadding * 2) / 2;
      });
    
    // Update target label positions
    labels
      .filter(d => d.type === 'target')
      .each(function(d) {
        const g = d3.select(this);
        const nodeCenterX = (d.x0 + d.x1) / 2;
        const nodeCenterY = (d.y0 + d.y1) / 2;
        const nodeHeight = d.y1 - d.y0;
        const nodeWidth = d.x1 - d.x0;
        const targetLabel = d.name || d.targetLabel || '';
        const value = Math.round(d.value || 0);
        
        if (targetLabel) {
          const maxLabelWidth = nodeWidth * 0.8;
          const fontSize = Math.max(8, Math.min(11, nodeHeight / 6));
          const lineHeight = fontSize * 1.2;
          const wrappedLines = wrapText(targetLabel, maxLabelWidth, fontSize);
          const totalTextHeight = wrappedLines.length * lineHeight;
          const startY = nodeCenterY - (totalTextHeight / 2) + (lineHeight / 2);
          
          // Update text element
          let textElement = g.select("text").filter(function() {
            return !d3.select(this).select("tspan").empty() || d3.select(this).text() === targetLabel.split(' ')[0];
          });
          
          if (textElement.empty()) {
            textElement = g.append("text");
          }
          
          textElement
            .transition()
            .duration(750)
            .attr("x", nodeCenterX)
            .attr("y", startY)
            .attr("text-anchor", "middle")
            .attr("font-size", `${fontSize}px`)
            .attr("font-weight", "normal")
            .attr("fill", "#000");
          
          // Update tspans
          const tspans = textElement.selectAll("tspan")
            .data(wrappedLines);
          
          tspans.exit().remove();
          
          const tspansEnter = tspans.enter()
            .append("tspan");
          
          tspansEnter.merge(tspans)
            .transition()
            .duration(750)
            .attr("x", nodeCenterX)
            .attr("dy", (d, i) => i === 0 ? 0 : lineHeight)
            .text(d => d);
          
          // Update value text
          const valueTexts = g.selectAll("text").filter(function() {
            const text = d3.select(this).text();
            return !isNaN(parseInt(text)) && parseInt(text) === value;
          });
          
          if (nodeHeight > 30 && value > 0) {
            const valueY = nodeCenterY + (totalTextHeight / 2) + 12;
            if (valueTexts.empty()) {
              g.append("text")
                .attr("x", nodeCenterX)
                .attr("y", valueY)
                .attr("dy", "0.35em")
                .attr("text-anchor", "middle")
                .attr("font-size", `${Math.max(10, Math.min(14, nodeHeight / 4))}px`)
                .attr("font-weight", "bold")
                .attr("fill", "#fff")
                .attr("stroke", "#000")
                .attr("stroke-width", "0.5px")
                .attr("paint-order", "stroke")
                .style("pointer-events", "auto")
                .style("cursor", "pointer")
                .text(value.toString());
            } else {
              valueTexts
                .transition()
                .duration(750)
                .attr("x", nodeCenterX)
                .attr("y", valueY);
            }
          } else {
            valueTexts.remove();
          }
        }
      });
    
    // Update link label positions
    linkLabels
      .transition()
      .duration(750)
      .attr("x", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        return (source.x1 + target.x0) / 2;
      })
      .attr("y", d => {
        const source = nodes[d.source];
        const target = nodes[d.target];
        return (source.y0 + source.y1 + target.y0 + target.y1) / 4;
      });
  };
  
  // Click handler for isolation (show only clicked node and its connections)
  const handleNodeClick = function(event, d) {
    event.stopPropagation(); // Prevent zoom/pan
    
    const nodeIndex = d.index;
    
    // Toggle isolation: if clicking the same node, un-isolate; otherwise isolate this node
    if (isolatedNodeIndex === nodeIndex) {
      // Clicking the same node again - un-isolate (show all nodes)
      isolatedNodeIndex = null;
    } else {
      // Isolate this node (hide all non-related nodes)
      isolatedNodeIndex = nodeIndex;
    }
    
    // Clear selection when isolating/un-isolating
    selectedNodes.clear();
    
    // Update visual state based on isolation
    updateIsolation();
    
    // Redistribute nodes to fit the screen (reposition instead of zoom)
    redistributeNodes();
    
    // Show tooltip
    let tooltipText = "";
    if (d.type === 'company') {
      tooltipText = `<strong>${d.name}</strong><br/>Company`;
      if (isolatedNodeIndex === nodeIndex) {
        tooltipText += `<br/><em>Isolated - Click again to show all</em>`;
      }
    } else if (d.type === 'target') {
      tooltipText = `<strong>${d.name || d.targetLabel}</strong><br/>${dimension}`;
      if (dimension === 'Market' && d.state) {
        tooltipText += ` - ${d.state}`;
      }
      if (isolatedNodeIndex === nodeIndex) {
        tooltipText += `<br/><em>Isolated - Click again to show all</em>`;
      }
    }
    
    tooltip
      .html(tooltipText)
      .style("opacity", 1)
      .style("left", (event.pageX + 10) + "px")
      .style("top", (event.pageY - 10) + "px");
    
    // Hide tooltip after a moment
    setTimeout(() => {
      tooltip.style("opacity", 0);
    }, 1500);
  };
  
  // Add hover and click effects to labels (so text is also hoverable)
  labels
    .style("cursor", "pointer")
    .on("mouseover", handleNodeHover)
    .on("mousemove", function(event) {
      tooltip
        .style("left", (event.pageX + 10) + "px")
        .style("top", (event.pageY - 10) + "px");
    })
    .on("mouseout", handleNodeMouseout)
    .on("click", handleNodeClick);
  
  // Add hover and click effects to nodes
  node
    .on("mouseover", handleNodeHover)
    .on("mousemove", function(event) {
      tooltip
        .style("left", (event.pageX + 10) + "px")
        .style("top", (event.pageY - 10) + "px");
    })
    .on("mouseout", handleNodeMouseout)
    .on("click", handleNodeClick);
  
  // Add hover effects to links (only if nothing is selected)
  link
    .style("cursor", "pointer")
    .on("mouseover", function(event, d) {
      // Only show temporary highlight if nothing is isolated or selected
      if (isolatedNodeIndex !== null || selectedNodes.size > 0) {
        // Just show tooltip
        const sourceNode = nodes[d.source];
        const targetNode = nodes[d.target];
        let tooltipText = "";
        if (sourceNode && targetNode) {
          if (sourceNode.type === 'company') {
            tooltipText = `<strong>${sourceNode.name}</strong> → `;
          } else {
            let sourceLabel = `<strong>${sourceNode.name || sourceNode.targetLabel}</strong>`;
            if (dimension === 'Market' && sourceNode.state) {
              sourceLabel += ` (${sourceNode.state})`;
            }
            tooltipText = `${sourceLabel} (${sourceNode.year || ''}) → `;
          }
          
          if (targetNode.type === 'target') {
            let targetLabel = `<strong>${targetNode.name || targetNode.targetLabel}</strong>`;
            if (dimension === 'Market' && targetNode.state) {
              targetLabel += ` (${targetNode.state})`;
            }
            tooltipText += `${targetLabel} (${targetNode.year || ''})`;
          } else {
            tooltipText += `<strong>${targetNode.name}</strong>`;
          }
          
          tooltipText += `<br/>Value: ${Math.round(d.value)}`;
          if (d.isContinuous) {
            if (d.valueChange !== undefined) {
              const change = Math.round(d.valueChange);
              const percent = Math.round(d.valueChangePercent);
              if (change > 0) {
                tooltipText += `<br/>Change: +${change} (+${percent}%)`;
              } else if (change < 0) {
                tooltipText += `<br/>Change: ${change} (${percent}%)`;
              } else {
                tooltipText += `<br/>Change: Stable`;
              }
            }
          }
        }
        tooltip
          .html(tooltipText)
          .style("opacity", 1)
          .style("left", (event.pageX + 10) + "px")
          .style("top", (event.pageY - 10) + "px");
        return;
      }
      
      // Temporary highlight (only when nothing is selected)
      const sourceNode = nodes[d.source];
      const targetNode = nodes[d.target];
      const relatedNodes = new Set([d.source, d.target]);
      const relatedLinks = new Set([d.originalIndex]);
      
      link
        .transition()
        .duration(200)
        .attr("fill-opacity", (d) => relatedLinks.has(d.originalIndex) ? 0.95 : 0.1)
        .attr("stroke-opacity", (d) => relatedLinks.has(d.originalIndex) ? 1.0 : 0.1)
        .attr("stroke-width", (d) => relatedLinks.has(d.originalIndex) ? 
          (d.isContinuous ? Math.max(3, Math.min(6, Math.sqrt(d.value) * 3)) : Math.max(2, Math.min(5, Math.sqrt(d.value) * 2.5))) : 
          (d.isContinuous ? Math.max(1, Math.min(2, Math.sqrt(d.value) * 1)) : Math.max(1, Math.min(1.5, Math.sqrt(d.value) * 1))));
      
      node
        .selectAll("rect, circle")
        .transition()
        .duration(200)
        .attr("fill-opacity", function() {
          const parentNode = d3.select(this.parentNode).datum();
          if (!parentNode || parentNode.index === undefined) return 0.1;
          return relatedNodes.has(parentNode.index) ? 0.9 : 0.1;
        })
        .attr("stroke-opacity", function() {
          const parentNode = d3.select(this.parentNode).datum();
          if (!parentNode || parentNode.index === undefined) return 0.2;
          const isCircle = this.tagName === 'circle';
          // Keep circles' stroke more visible even when dimmed
          if (isCircle) {
            return relatedNodes.has(parentNode.index) ? 1.0 : 0.6;
          }
          return relatedNodes.has(parentNode.index) ? 1.0 : 0.2;
        })
        .attr("stroke-width", function() {
          const parentNode = d3.select(this.parentNode).datum();
          if (!parentNode || parentNode.index === undefined) return 1;
          return relatedNodes.has(parentNode.index) ? 2 : 1;
        });
      
      linkLabels
        .transition()
        .duration(200)
        .attr("opacity", (labelD) => {
          const linkIdx = labelD.linkIndex !== undefined ? labelD.linkIndex : -1;
          return relatedLinks.has(linkIdx) ? 1 : 0.2;
        });
      
      // Show tooltip for link
      let tooltipText = "";
      if (sourceNode && targetNode) {
        if (sourceNode.type === 'company') {
          tooltipText = `<strong>${sourceNode.name}</strong> → `;
        } else {
          tooltipText = `<strong>${sourceNode.name || sourceNode.targetLabel}</strong> (${sourceNode.year || ''}) → `;
        }
        
        if (targetNode.type === 'target') {
          tooltipText += `<strong>${targetNode.name || targetNode.targetLabel}</strong> (${targetNode.year || ''})`;
        } else {
          tooltipText += `<strong>${targetNode.name}</strong>`;
        }
        
        tooltipText += `<br/>Value: ${Math.round(d.value)}`;
        if (d.isContinuous) {
          if (d.valueChange !== undefined) {
            const change = Math.round(d.valueChange);
            const percent = Math.round(d.valueChangePercent);
            if (change > 0) {
              tooltipText += `<br/>Change: +${change} (+${percent}%)`;
            } else if (change < 0) {
              tooltipText += `<br/>Change: ${change} (${percent}%)`;
            } else {
              tooltipText += `<br/>Change: Stable`;
            }
          }
        }
      }
      
      tooltip
        .html(tooltipText)
        .style("opacity", 1)
        .style("left", (event.pageX + 10) + "px")
        .style("top", (event.pageY - 10) + "px");
    })
    .on("mousemove", function(event) {
      tooltip
        .style("left", (event.pageX + 10) + "px")
        .style("top", (event.pageY - 10) + "px");
    })
    .on("mouseout", function() {
      // Only reset if nothing is isolated or selected
      if (isolatedNodeIndex === null && selectedNodes.size === 0) {
        updateIsolation(); // This will reset to default state
      }
      tooltip.style("opacity", 0);
    });
  
  // Update SVG to accommodate full content height
  svg.attr("viewBox", [0, 0, width, actualHeight])
     .attr("height", actualHeight);
  
  // Add year labels at top
  const yearLabels = [null, 2020, 2021, 2022, 2023, 2024];
  yearLabels.forEach((year, i) => {
    if (year !== null) {
      container.append("text")
        .attr("x", columnX(i) + columnWidth / 2)
        .attr("y", margin.top + 20)
        .attr("text-anchor", "middle")
        .attr("font-size", "14px")
        .attr("font-weight", "bold")
        .text(year);
    }
  });
  
  // Add column header
  container.append("text")
    .attr("x", columnX(0) + columnWidth / 2)
    .attr("y", margin.top + 20)
    .attr("text-anchor", "middle")
    .attr("font-size", "14px")
    .attr("font-weight", "bold")
    .text("Companies");
  
  // Add legend for continuity (top right corner)
  const legendY = margin.top + 50;
  const legendX = width - 200;
  
  // Continuous links (same target.label) - Green for increase
  container.append("rect")
    .attr("x", legendX)
    .attr("y", legendY)
    .attr("width", 15)
    .attr("height", 10)
    .attr("fill", "#4ade80")
    .attr("stroke", "#22c55e")
    .attr("stroke-width", 1);
  container.append("text")
    .attr("x", legendX + 20)
    .attr("y", legendY + 8)
    .attr("font-size", "10px")
    .attr("fill", "#000")
    .text("Continuity: Value Increase");
  
  // Continuous links - Orange for decrease
  container.append("rect")
    .attr("x", legendX)
    .attr("y", legendY + 15)
    .attr("width", 15)
    .attr("height", 10)
    .attr("fill", "#fb923c")
    .attr("stroke", "#f97316")
    .attr("stroke-width", 1);
  container.append("text")
    .attr("x", legendX + 20)
    .attr("y", legendY + 23)
    .attr("font-size", "10px")
    .attr("fill", "#000")
    .text("Continuity: Value Decrease");
  
  // Continuous links - Blue for stable
  container.append("rect")
    .attr("x", legendX)
    .attr("y", legendY + 30)
    .attr("width", 15)
    .attr("height", 10)
    .attr("fill", dimension === 'Risk' ? '#ff6b6b' : dimension === 'Opportunity' ? '#4ecdc4' : '#45b7d1')
    .attr("stroke", dimension === 'Risk' ? '#ff4444' : dimension === 'Opportunity' ? '#2eb8b3' : '#2ea3d1')
    .attr("stroke-width", 1);
  container.append("text")
    .attr("x", legendX + 20)
    .attr("y", legendY + 38)
    .attr("font-size", "10px")
    .attr("fill", "#000")
    .text("Continuity: Stable Value");
  
  // Add pan/zoom behavior (allow programmatic zoom for auto-fit and pinch gesture)
  // Filter to only allow pan on background (not on nodes/links), disable wheel zoom but allow pinch
  const zoom = d3.zoom()
    .scaleExtent([0.1, 3]) // Allow zoom from 10% to 300% (for auto-fit and pinch)
    .filter(function(event) {
      // Disable wheel zoom (only allow programmatic zoom and pinch gesture)
      if (event.type === 'wheel') {
        return false;
      }
      // Allow touch events for pinch gesture
      if (event.type === 'touchstart' || event.type === 'touchmove' || event.type === 'touchend') {
        // For pinch gesture, we need at least 2 touches
        if (event.touches && event.touches.length === 2) {
          return true; // Allow pinch zoom
        }
        // For single touch, check if it's on interactive elements
        const target = event.target;
        const tagName = target.tagName?.toLowerCase();
        if (tagName === 'text' || tagName === 'rect' || tagName === 'circle' || tagName === 'path') {
          const parent = target.parentElement;
          if (parent && (parent.classList.contains('node-labels') || 
                         parent.classList.contains('links') ||
                         parent.querySelector('rect, circle'))) {
            return false; // Prevent pan on nodes/links
          }
        }
        return true; // Allow single touch pan on background
      }
      // Allow pan/zoom on background, but not on interactive elements
      // Return true to allow pan, false to prevent it
      const target = event.target;
      const tagName = target.tagName?.toLowerCase();
      // Prevent pan if clicking directly on interactive elements
      if (tagName === 'text' || tagName === 'rect' || tagName === 'circle' || tagName === 'path') {
        // Check if it's part of a node or link group
        const parent = target.parentElement;
        if (parent && (parent.classList.contains('node-labels') || 
                       parent.classList.contains('links') ||
                       parent.querySelector('rect, circle'))) {
          return false; // Prevent pan on nodes/links
        }
      }
      return true; // Allow pan on background/SVG
    })
    .on("zoom", function(event) {
      container.attr("transform", event.transform);
    });
  
  // Apply zoom to the SVG
  svg.call(zoom)
    .style("cursor", "grab");
  
  // Change cursor on drag (only on background)
  svg.on("mousedown.zoom", function(event) {
    // Only change cursor if not clicking on interactive elements
    if (!event.target.closest("g[class*='node'], path[class*='link'], text")) {
      svg.style("cursor", "grabbing");
    }
  })
  .on("mouseup.zoom", function(event) {
    svg.style("cursor", "grab");
    
    // Clear isolation and selection if clicking on background
    const target = event.target;
    if (target === svg.node() || target.tagName?.toLowerCase() === 'svg') {
      isolatedNodeIndex = null;
      selectedNodes.clear();
      updateIsolation();
      redistributeNodes(); // Reset positions to original
    }
  })
  .on("mouseleave.zoom", function() {
    svg.style("cursor", "grab");
  });
  
  // Initialize state (no nodes isolated or selected initially)
  updateIsolation();
  
  // Watch for clear selection button clicks
  // Make chart reactive to clearSelectionTrigger
  const currentClearTrigger = clearSelectionTrigger;
  
  // Clear selection if button was clicked
  if (currentClearTrigger > 0) {
    isolatedNodeIndex = null;
    selectedNodes.clear();
    updateIsolation();
    redistributeNodes();
  }
  
  return svg.node();
})()
```

<!--{"pinCode":false,"dname":"27a9b411-886e-4c89-a060-31a5ca2f1645","codeMode":"js"}-->
```js
mutable clearSelectionTrigger = null;
```
