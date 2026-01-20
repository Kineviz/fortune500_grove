<!--{"pinCode":false,"dname":"130dea9d-a31e-43df-8d9b-a68ddf2a1f13","codeMode":"js"}-->
```js
function canonicalizeMarket(raw) {
  const rules = [
    // --- Core technology domains ---
    {
      key: "metaverse_xr",
      label: "Metaverse & Extended Reality (AR/VR/MR)",
      match: /(metaverse|augmented reality|virtual reality|mixed reality|spatial computing|xr|smart glasses)/i
    },
    {
      key: "artificial_intelligence",
      label: "Artificial Intelligence & Generative AI",
      match: /(artificial intelligence|ai|generative ai|personal intelligence)/i
    },
    {
      key: "cloud_services",
      label: "Cloud Computing & Infrastructure",
      match: /(cloud services|enterprise cloud|aws|google cloud|satellite broadband|global broadband)/i
    },
    {
      key: "consumer_devices",
      label: "Consumer Devices & Hardware",
      match: /(consumer hardware|consumer devices|electronic devices|hardware|smartphones|personal computers|tablets|mac|home devices|accessories)/i
    },
    {
      key: "wearables",
      label: "Wearables & Accessories",
      match: /(wearables|wearable devices|wearables hardware)/i
    },
    {
      key: "health_technology",
      label: "Health & Life Sciences Technology",
      match: /(healthcare|health technology|health applications|health services|life sciences|personalized fitness)/i
    },
    {
      key: "transportation_autonomous",
      label: "Transportation & Autonomous Mobility",
      match: /(transportation|autonomous|ride-hailing|advanced transportation)/i
    },
    {
      key: "media_content",
      label: "Media, Content & Streaming",
      match: /(media content|original digital content|sports subscription|youtube|stories format|giphy)/i
    },
    {
      key: "advertising_marketing",
      label: "Advertising, Marketing & Social Commerce",
      match: /(advertising|ad formats|social commerce|ai-powered social)/i
    },
    {
      key: "messaging_platforms",
      label: "Messaging & Communication Platforms",
      match: /(messaging|whatsapp|messenger|broadcast messaging|public conversations)/i
    },
    {
      key: "subscriptions",
      label: "Subscription Products & Services",
      match: /(subscription|non-advertising revenue|non-advertising businesses)/i
    },
    {
      key: "financial_services",
      label: "Financial Services & Payments",
      match: /(financial services|buy now, pay later)/i
    },
    {
      key: "retail_operations",
      label: "Retail, Fulfillment & Physical Stores",
      match: /(retail|physical stores|fulfillment network)/i
    },
    {
      key: "manufacturing_supply_chain",
      label: "Manufacturing & Supply Chain",
      match: /(manufacturing|supply chain)/i
    },
    {
      key: "cybersecurity",
      label: "Cybersecurity",
      match: /(cybersecurity)/i
    },
    {
      key: "public_sector",
      label: "Public Sector",
      match: /(public sector)/i
    },

    // --- Geographic markets ---
    {
      key: "asia_pacific",
      label: "Asia-Pacific",
      match: /(asia-pacific|india|philippines|bangladesh|indonesia|japan|greater china|vietnam)/i
    },
    {
      key: "emea",
      label: "Europe, Middle East & Africa (EMEA)",
      match: /(europe|eu|emea|russia|nigeria)/i
    },
    {
      key: "americas",
      label: "Americas",
      match: /(americas)/i
    },
    {
      key: "international_markets",
      label: "International & Emerging Markets",
      match: /(international|emerging markets|new geographic regions|global operations)/i
    }
  ];

  for (const rule of rules) {
    if (rule.match.test(raw)) {
      return {
        canonicalKey: rule.key,
        canonicalLabel: rule.label
      };
    }
  }

  // Fallback: stable, deterministic key
  return {
    canonicalKey: raw
      .toLowerCase()
      .replace(/[^a-z0-9]+/g, "_")
      .replace(/^_|_$/g, ""),
    canonicalLabel: raw.trim()
  };
}
```

<!--{"pinCode":false,"dname":"c74ddd1a-6d32-473f-b453-3ac74c9e9dc3","codeMode":"js"}-->
```js
simplifyMarket = ()=>{
  let nodes = gxr.nodes().filter(n=>n.category=='Market')
//   return nodes.array
  nodes.array.forEach(
    n=>{
      let risk = canonicalizeMarket(n.properties.label)
      n.properties['marketKey'] = risk.canonicalKey
      n.properties['marketLabel'] = risk.canonicalLabel     
    }
  )
}
```

<!--{"pinCode":false,"dname":"23f302cd-dd2a-4541-9720-79a6f991dcb0","codeMode":"js"}-->
```js
Inputs.button("Perform Mapping", {reduce: ()=>simplifyMarket
})
```

<!--{"pinCode":false,"dname":"8262a36f-49c3-40a1-9b48-ccbf4d497d16","codeMode":"js"}-->
```js
canonicalizeMarket(gxr.nodes().array[0].properties.label)
```

<!--{"pinCode":true,"dname":"996fdaa8-fd39-4f79-83a6-eee7435211a6","codeMode":"markdown"}-->

