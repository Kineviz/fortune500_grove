<!--{"type":"header","level":2}-->
## Test simple markd

<!--{"pinCode":false,"dname":"e526fcda-46f7-4a19-9c20-560e7cde320d","codeMode":"markdown"}-->
### Utility functions

<!--{"pinCode":true,"dname":"96af62eb-84b3-4ff2-814d-ccc6af114568","codeMode":"js"}-->
```js
propagateCompanyName = ()=>
{
//   Add Company:id => Company:company
  gxr.nodes(':Company').forEach(c=>{
    c.properties.company = c.properties.id
  })
//   Add Company:id => Market/Risk/Competitor:company
  let myedges = gxr.edges().array.filter(e=>['Market', 'Risk', 'Competitor'].includes(e.target.category))
  myedges.forEach(e=>{
    e.target.properties.company = e.source.properties.id
  })

}
```

<!--{"pinCode":true,"dname":"438e5a6f-4e03-4386-a227-0d7a1026ba8c","codeMode":"js"}-->
```js

```

<!--{"pinCode":true,"dname":"cb676e21-7baf-48ec-b9ba-aadaed712fce","codeMode":"js"}-->
```js
myedges[1].source.properties
```
