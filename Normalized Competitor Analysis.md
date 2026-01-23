### Competitor Cohort Analysis

dienert test eh uma caba muito doidão, meu! claro! que sim! então? test, nice

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
normalized_to_edges = gxr.edges({relationship:'AS'}).toArray()
```

<!--{"pinCode":false,"dname":"96fc2d80-8336-474e-9eb6-7a865533c646","codeMode":"js","hide":true}-->
```js
normalized_to_edges[0].targetId
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
  if(target_competitor_category_node.normalizedRisk){
    let competitor = target_competitor_category_node
      .normalizedRisk.properties.normalized_competitor
    
    return md `
## ${competitor}   
    `
  
  }
    
    return md ``

}
```

<!--{"pinCode":false,"dname":"1f40b519-17d5-4008-84ea-e41903983492","codeMode":"js"}-->
```js
{
  if(by_year.length>1){
    return Plot.plot({
      height: 250,
      margin: 40,
//       title: "Occurrences by Year",
      style: {
        fontSize: 14,
        "--plot-title-font-size": "10px"
      },
      marks: [
        Plot.ruleY([0]),
        Plot.line(by_year, {x: "year", y: "count", stroke: "steelblue"}),
        Plot.dot(by_year, {x: "year", y: "count", fill: "steelblue", r: 4, tip: true})
      ],
      x: {
        label: "Year",
        tickFormat: "d"
      },
      y: {
        label: "Count",
        grid: true
      }
    })
  } else {
    return `Select a NormalizedRisk node to see the trend`
  }
}
```

<!--{"pinCode":false,"dname":"3ea81eaf-578e-4c80-8720-efebb6298621","codeMode":"js"}-->
```js
{
  if(target_competitor_category_node.normalizedRisk){
    let competitor = target_competitor_category_node
      .normalizedRisk.properties.normalized_competitor
    let occurrence = target_competitor_category_node
      .normalizedRisk.properties.occurrence
    let associated_companies = target_competitor_category_node
      .normalizedRisk.properties.associated_companies
    let tickers = _.uniq(target_competitor_category_node.linked_competitors
      .map(n=>n.properties.ticker))
    let companies = _.uniq(target_competitor_category_node.linked_competitors
      .map(n=>n.properties.company_name))
//     let data = {risk, occurrence, tickers}
    return md `
${occurrence} Occurrence  
${associated_companies} Associated Companies
${companies.map(t => `- ${t}`).join('\n')}
   
    `
  
  }
    
    return md ``

}
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
