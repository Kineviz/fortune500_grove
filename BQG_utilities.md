<!--{"type":"header","level":1}-->
# Test markdown test from Dienert

<!--{"pinCode":true,"dname":"0fd72d03-b4f4-4b5f-a142-04cf71fd1f15","codeMode":"js"}-->
```js
// {
// 	let query=`MATCH (s:Site) return s.name limit 5`
//     return BQGQueryToTable(query)
// }
```

<!--{"pinCode":true,"dname":"1fdb36d0-59c1-44ab-b261-57207bcbb792","codeMode":"js"}-->
```js
BQGQueryToTable = async (query) =>{
  let res = await gxr.query(query, {saveToGraph : false})
  return parseQueryTable(res.data)
}
```

<!--{"pinCode":false,"dname":"0bcd5c46-ac38-4ed7-a675-1f8dd5af76bd","codeMode":"js"}-->
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
