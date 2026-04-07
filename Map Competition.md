<!--{"pinCode":false,"dname":"130dea9d-a31e-43df-8d9b-a68ddf2a1f13","codeMode":"js"}-->
```js
function canonicalizeCompetitor(raw) {
  const s0 = String(raw ?? "").trim();
  if (!s0) return { canonicalKey: "unknown", canonicalLabel: "Unknown" };

  // normalize punctuation/case and strip parentheticals for matching
  const s = s0
    .toLowerCase()
    .replace(/[’‘]/g, "'")
    .replace(/[&]/g, " and ")
    .replace(/[\u2013\u2014]/g, "-")
    .replace(/\s*\([^)]*\)\s*/g, " ")
    .replace(/[^a-z0-9]+/g, " ")
    .replace(/\s+/g, " ")
    .trim();

  const entries = [
    // --- specific companies / brands (map first) ---
    { k: "company:alphabet", l: "Alphabet", a: ["alphabet", "google llc"] },
    { k: "company:google", l: "Google", a: ["google"] },
    { k: "company:youtube", l: "YouTube", a: ["youtube"] },
    { k: "company:meta", l: "Meta", a: ["meta", "facebook"] },
    { k: "company:amazon", l: "Amazon", a: ["amazon"] },
    { k: "company:apple", l: "Apple", a: ["apple"] },
    { k: "company:microsoft", l: "Microsoft", a: ["microsoft"] },
    { k: "company:salesforce", l: "Salesforce", a: ["salesforce"] },
    { k: "company:tencent", l: "Tencent", a: ["tencent", "wechat"] },
    { k: "company:bytedance", l: "ByteDance", a: ["bytedance"] },
    { k: "company:tiktok", l: "TikTok", a: ["tiktok"] },
    { k: "company:snap", l: "Snap", a: ["snap", "snapchat"] },
    { k: "company:twitter", l: "Twitter", a: ["twitter"] },
    { k: "company:alibaba", l: "Alibaba", a: ["alibaba"] },
    { k: "company:baidu", l: "Baidu", a: ["baidu"] },
    { k: "company:netflix", l: "Netflix", a: ["netflix"] },
    { k: "company:anthropic", l: "Anthropic", a: ["anthropic", "anthropic pbc"] },
    { k: "company:rivian", l: "Rivian", a: ["rivian"] },
    { k: "company:fitbit", l: "Fitbit", a: ["fitbit"] },
    { k: "company:epic_games", l: "Epic Games", a: ["epic games"] },

    // --- platforms / ecosystems ---
    { k: "platform:android", l: "Android Platform", a: ["android", "competitors on the android platform"] },
    { k: "platform:windows", l: "Windows Platform", a: ["windows", "competitors on the windows platform"] },
    { k: "platform:gaming_consoles", l: "Gaming Consoles (PlayStation/Nintendo/Xbox)", a: ["playstation", "nintendo", "xbox", "playstation nintendo and xbox"] },

    // --- category buckets (collapse variants / plurals) ---
    { k: "cat:retail", l: "Retailers (Physical / E-commerce / Omnichannel)", a: ["retailer", "retail competitors", "physical e commerce and omnichannel retailers", "retailers physical e commerce omnichannel"] },
    { k: "cat:ecommerce_services", l: "E-commerce Service Providers", a: ["e commerce service provider", "ecommerce service provider"] },
    { k: "cat:consumer_electronics", l: "Consumer Electronics Companies", a: ["consumer electronics", "consumer hardware manufacturers"] },
    { k: "cat:hardware_vr_ar", l: "Consumer Hardware & VR/AR Companies", a: ["vr", "ar", "consumer hardware and vr ar companies"] },
    { k: "cat:it_cloud", l: "IT & Cloud Service Providers", a: ["it and cloud service providers", "it service and cloud providers", "providers of enterprise cloud services", "competitors in cloud services"] },
    { k: "cat:it_services_products", l: "IT Service & Product Companies", a: ["information technology service and product companies", "information technology service providers"] },
    { k: "cat:web_infra_compute", l: "Web & Infrastructure Computing Services", a: ["web and infrastructure computing services", "web and infrastructure computing services providers", "web and infrastructure computing services competitors"] },
    { k: "cat:fulfillment_logistics", l: "Fulfillment & Logistics Providers", a: ["fulfillment", "logistics", "fulfillment and logistics service companies", "fulfillment and logistics providers"] },
    { k: "cat:grocery", l: "Grocery Retailers", a: ["grocery companies", "grocery retailers"] },
    { k: "cat:advertising_services", l: "Advertising Service Providers", a: ["advertising service providers", "advertising companies"] },
    { k: "cat:ad_platforms", l: "Online Advertising Platforms & Networks", a: ["online advertising platforms and networks"] },
    { k: "cat:traditional_ad_media", l: "Traditional Advertising Media", a: ["traditional advertising media", "billboards", "magazines"] },
    { k: "cat:media_content", l: "Media & Content Companies", a: ["media companies", "media and content companies", "media publishers producers and distributors", "media producers and distributors"] },
    { k: "cat:digital_video", l: "Digital Video Service Providers", a: ["digital video services", "providers of digital video services"] },
    { k: "cat:search_general", l: "General-Purpose Search Engines", a: ["general purpose search engines", "general purpose search engines and information services"] },
    { k: "cat:search_social", l: "Web Search & Social Networks", a: ["web search engines social networks and web portals", "web search engines and social networks"] },
    { k: "cat:web_discovery", l: "Web Discovery Platforms", a: ["web discovery platforms", "online connection sharing and discovery companies"] },
    { k: "cat:vertical_search_ecom", l: "Vertical Search & E-commerce Sites", a: ["vertical search", "vertical search e commerce sites", "vertical search engines and e commerce providers", "vertical search and e commerce sites"] },
    { k: "cat:digital_platforms", l: "Digital Content/App Platform Providers", a: ["digital content and application platform providers"] },
    { k: "cat:digital_assistants", l: "Digital Assistant Providers", a: ["digital assistant providers"] },
    { k: "cat:workspace_collab", l: "Workspace Communication/Connectivity Products", a: ["providers of workspace communication and connectivity products"] },
    { k: "cat:ai_products_services", l: "AI Product & Service Providers", a: ["developers and providers of ai products and services"] },
    { k: "cat:internet_access", l: "Internet Access Providers", a: ["internet access providers"] },
    { k: "cat:patent_holders", l: "Patent-Holding Companies", a: ["patent holding companies"] },

    // --- “vague/strategic” competitor language ---
    { k: "comp:unnamed", l: "Unnamed Competitors", a: ["unnamed competitors", "unnamed competing products and services", "unnamed competitive products and services"] },
    { k: "comp:adjacent_markets", l: "Adjacent Market Competitors", a: ["adjacent market", "companies from adjacent market segments", "established companies from other market segments", "cross industry competitors"] },
    { k: "comp:international_local", l: "Local/International Competitors", a: ["local international companies", "local companies in international markets", "local companies in international markets"] },
    { k: "comp:new_entrants", l: "New Entrants / Well-Funded Startups", a: ["new and well funded entrants", "new market entrants", "emerging start ups", "competitors in new ventures"] },
    { k: "comp:large_established", l: "Large / Established Competitors", a: ["large established competitors", "large and established companies", "established and well resourced companies", "established service providers"] },
    { k: "comp:low_cost", l: "Low-Cost Competitors / Imitators", a: ["low cost competitors", "low cost imitators", "low cost hardware manufacturers"] },
    { k: "comp:free_models", l: "Free-Model Digital Service Providers", a: ["digital service providers with free models"] },
  ];

  // helper: title case for unknown company names
  const titleCase = (x) =>
    x.replace(/\w\S*/g, (t) => t.charAt(0).toUpperCase() + t.slice(1).toLowerCase());

  // 1) exact/alias match
  for (const e of entries) {
    if (e.a.some((a) => s === a || s.includes(a))) return { canonicalKey: e.k, canonicalLabel: e.l };
  }

  // 2) heuristic: if it looks like a single proper-name company, treat as company
  // (e.g., "SomeCo", "ACME Inc" after normalization)
  if (!/providers|companies|services|platforms|retailers|competitors|engines|networks|media|publishers|distributors|manufacturers|entrants|imitators|startups|segments|markets/.test(s)) {
    const key = "company:" + s.replace(/\s+/g, "_");
    return { canonicalKey: key, canonicalLabel: titleCase(s0.trim()) };
  }

  // 3) fallback: keep as category-ish
  return { canonicalKey: "cat:" + s.replace(/\s+/g, "_"), canonicalLabel: titleCase(s0.trim()) };
}
```

<!--{"pinCode":false,"dname":"23f302cd-dd2a-4541-9720-79a6f991dcb0","codeMode":"js"}-->
```js
Inputs.button("Perform Mapping", {reduce: ()=>
 {
  let nodes = gxr.nodes().filter(n=>n.category=='Competitor')
//   return nodes.array
  nodes.array.forEach(
    n=>{
      let risk = canonicalizeCompetitor(n.properties.label)
      n.properties['competitorKey'] = risk.canonicalKey
      n.properties['competitorLabel'] = risk.canonicalLabel     
    }
  )
}
})
```

<!--{"pinCode":false,"dname":"8262a36f-49c3-40a1-9b48-ccbf4d497d16","codeMode":"js"}-->
```js
canonicalizeCompetitor(gxr.nodes().array[0].properties.label)
```
