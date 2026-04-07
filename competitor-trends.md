# Company Competitor Trends

This notebook allows you to search for a company and visualize the trend of other companies claiming it as a competitor over time.

## 1. Search for a Company
Enter the name of the company you want to investigate (e.g., "Google" or "Microsoft").

<!--{"pinCode":true,"dname":"search-input","codeMode":"js","hide":false}-->
```js
viewof targetCompany = Inputs.text({
  label: "Search Company", 
  placeholder: "e.g. Google", 
  value: "Google"
})
```

<!--{"pinCode":false,"dname":"fetch-data","codeMode":"js","hide":false}-->
```js
results = {
  if (!targetCompany) return [];
  
  // Kuzu-compatible query using UNION to find direct and indirect competitors
  const query = `
    MATCH (c:Company)-[r1:COMPETES_WITH]->(comp:Competitor)
    WHERE comp.label CONTAINS "${targetCompany}"
    RETURN c, r1, comp
    UNION
    MATCH (c:Company)-[r1:COMPETES_WITH]->(comp:Competitor)-[r2:INSTANCE_OF]->(norm:NormalizedCompetitor)
    WHERE norm.label CONTAINS "${targetCompany}"
    RETURN c, r1, comp, r2, norm
    UNION
    MATCH (c:Company)-[r1:COMPETES_WITH]->(comp:Competitor)-[r2:INSTANCE_OF]->(norm:NormalizedCompetitor)-[r3:SUBSIDIARY_OF]->(parent:NormalizedCompetitor)
    WHERE parent.label CONTAINS "${targetCompany}"
    RETURN c, r1, comp, r2, norm, r3, parent
  `;
  
  await gxr.clear();
  await gxr.query(query);
  await gxr.forceLayout();
  
  // Small delay to ensure graph state is updated
  await gxr.sleep(500);
  
  // Extract data for the trend line from the current canvas
  const competitors = gxr.nodes({category: "Competitor"}).filter(n => {
    const label = (n.properties.label || "").toLowerCase();
    return label.includes(targetCompany.toLowerCase());
  });

  const targetIds = new Set(competitors.ids());
  const yearCounts = {};

  gxr.edges({relationship: "COMPETES_WITH"}).forEach(edge => {
    if (targetIds.has(edge.targetId)) {
      const compNode = gxr.getNode(edge.targetId);
      const year = compNode.properties.year;
      if (year) {
        yearCounts[year] = (yearCounts[year] || new Set());
        yearCounts[year].add(edge.sourceId);
      }
    }
  });

  return Object.entries(yearCounts)
    .map(([year, companySet]) => ({ year: parseInt(year), count: companySet.size }))
    .sort((a, b) => a.year - b.year);
}
```

## 2. Competition Trend
The chart below shows how many companies reported the target as a competitor each year.

<!--{"pinCode":false,"dname":"trend-chart","codeMode":"js","hide":false}-->
```js
{
  if (results.length === 0) return md`*No competition data found for "${targetCompany}"*`;
  
  return Plot.plot({
    marks: [
      Plot.line(results, {x: "year", y: "count", stroke: "steelblue", strokeWidth: 3}),
      Plot.dot(results, {x: "year", y: "count", fill: "white", stroke: "steelblue", r: 5}),
      Plot.text(results, {x: "year", y: "count", text: "count", dy: -15})
    ],
    x: { tickFormat: "d", label: "Year" },
    y: { grid: true, label: "Number of Companies" },
    title: `Trend: Companies competing with ${targetCompany}`
  });
}
```
