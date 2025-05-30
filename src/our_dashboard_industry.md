---
title: 🏭 Industry Specific Analysis
toc: false
---

<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h1 style="text-align: center;">Global Cybersecurity Threats: Industry Specific Analysis 🏭</h1>
</div>


```js
const data = FileAttachment("data/Global_Cybersecurity_Threats_2015-2024.csv").csv({typed: true});
```

```js
const data_clean = data.map(d => ({
  ...d,
  Country:
    d.Country === "UK"
      ? "United Kingdom"
      : d.Country === "USA"
      ? "United States of America"
      : d.Country
}))
```

```js
const data_clean_withLossPerUser = data_clean.map(d => ({
  ...d,
  ["Loss per User"]: d["Number of Affected Users"] > 0
    ? (d["Financial Loss (in Million $)"] / d["Number of Affected Users"]) * 1000000
    : undefined
}))
```


```js
const country_select = Inputs.select(
  ["All", ...new Set(data_clean_withLossPerUser.map(d => d.Country))],
  { label: "Select a Country" }
)
```

```js
const  industry_select = Inputs.select(
  ["All", ...new Set(data_clean_withLossPerUser.map(d => d["Target Industry"]))],
  { label: "Select an Industry" }
)
```

```js
const industry_select_value = Generators.input(industry_select)
  ```


```js
const country_select_value = Generators.input(country_select)
  ```

```js
const data_clean_groupedByYear = Array.from(
  d3.rollups(
    data_clean_withLossPerUser.filter(d =>
      (country_select_value === "All" || d.Country === country_select_value) &&
      (industry_select_value === "All" || d["Target Industry"] === industry_select_value)
    ),
    v => ({
      totalLoss: d3.sum(v, d => d["Financial Loss (in Million $)"]),
      count: v.length
    }),
    d => d.Year
  ),
  ([year, stats]) => ({
    Year: year,
    Loss: stats.totalLoss,
    Count: stats.count,
    tooltipLoss: `📅 Year: ${year}\n💰 Financial Loss: $${stats.totalLoss.toFixed(1)}M`,
    tooltipCount: `📅 Year: ${year}\n🚨 Number of Attacks: ${stats.count}`
  })
).sort((a, b) => a.Year - b.Year);
```

```js
const maxCount_attacks_filtered = d3.max(data_clean_groupedByYear, d => d["Count"])
```

```js
function attack_type_retail(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 2200],
      ticks: d3.range(0, 2001, 400),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Retail"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Type"]
            ),
            ([attack_type, meanLoss]) => ({ attack_type, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Type: ${d.attack_type}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_type",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```

```js
function attack_type_education(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 2200],
      ticks: d3.range(0, 2001, 400),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Education"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Type"]
            ),
            ([attack_type, meanLoss]) => ({ attack_type, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Type: ${d.attack_type}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_type",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```

```js
function attack_type_government(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 2200],
      ticks: d3.range(0, 2001, 400),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Government"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Type"]
            ),
            ([attack_type, meanLoss]) => ({ attack_type, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Type: ${d.attack_type}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_type",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```


```js
function attack_type_telecommunications(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 2200],
      ticks: d3.range(0, 2001, 400),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Telecommunications"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Type"]
            ),
            ([attack_type, meanLoss]) => ({ attack_type, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Type: ${d.attack_type}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_type",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```

<div style="width: 100%; margin-bottom: 20px; ">
As the findings at the global and country levels suggested patterns of repeated targeting in certain sectors, these were analysed in more detail.
</div>

<div style="width: 100%; margin-bottom: 40px;">
Some industries are consistently more exposed to cyber threats than others. The following sectors stood out due to their significant vulnerability to specific attack types or sources. Recognizing these weaknesses enables more precise and effective protective measures.
</div>

<div style="height: 1px; background-color: #922847; margin: 1em 0;"></div>


<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h2 style="text-align: center;">Attack Type</h2>
</div>


<div style="width: 100%;">
Firstly, the most harmful types of attack for each of the selected industries were explored and identified: Retail, Education, Government and Telecommunications.
<div>

<div class="grid grid-cols-2">
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Retail
        </div>
    ${resize((width) => attack_type_retail(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Education
        </div>
    ${resize((width) => attack_type_education(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Government
        </div>
    ${resize((width) => attack_type_government(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Telecommunications
        </div>
    ${resize((width) => attack_type_telecommunications(data_clean_withLossPerUser, {width}))}
  </div>
</div>

<div style="width: 100%;">
For each industry, it was possible to clearly determine the most harmful attack type:
</div>

Retail ➡️ Man-in-the-Middle

Education ➡️ Malware

Government ➡️ SQL Injection

Telecommunications ➡️ Malware and DDoS

<div style="width: 100%; margin-bottom: 40px;">
Once the attack type is identified, it becomes essential to examine the primary sources of these threats. Knowing the origin of an attack is key to forming comprehensive and effective cybersecurity strategies.
</div>



```js
function attack_source_retail(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 3000],
      ticks: d3.range(0, 2999, 500),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Retail"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Source"]
            ),
            ([attack_source, meanLoss]) => ({ attack_source, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Source: ${d.attack_source}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_source",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```

```js
function attack_source_education(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 3000],
      ticks: d3.range(0, 2999, 500),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Education"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Source"]
            ),
            ([attack_source, meanLoss]) => ({ attack_source, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Source: ${d.attack_source}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_source",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```

```js
function attack_source_government(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 3000],
      ticks: d3.range(0, 2999, 500),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Government"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Source"]
            ),
            ([attack_source, meanLoss]) => ({ attack_source, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Source: ${d.attack_source}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_source",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```


```js
function attack_source_telecommunications(data, {width}) {
  return Plot.plot({
    width,
    height: 300,
    marginLeft: 50,
    marginBottom: 50,
    style: {
      fontWeight: "bold",
      fontSize: "8px"
    },
    x: {
      label: null,
      tickSize: 0,
    },
    y: {
      grid: true,
      domain: [0, 3000],
      ticks: d3.range(0, 2999, 500),
      label: "↑ Avg. Loss per User (in $)"
    },
    color: {
      domain: ["normal", "highlight"],
      range: ["#cccccc", "#b33a52"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        (() => {
          const grouped = Array.from(
            d3.rollups(
              data.filter(d => d["Target Industry"] === "Telecommunications"),
              v => d3.mean(v, d => d["Loss per User"]),
              d => d["Attack Source"]
            ),
            ([attack_source, meanLoss]) => ({ attack_source, meanLoss })
          );
          const max = d3.max(grouped, d => d.meanLoss);
          return grouped.map(d => ({
            ...d,
            colorCategory: d.meanLoss > 800 ? "highlight" : "normal",
            tooltipTitle: `🎯 Attack Source: ${d.attack_source}\n💸 Avg. Loss/User: $${d.meanLoss.toFixed(2)}`
          }));
        })(),
        {
          x: "attack_source",
          y: "meanLoss",
          fill: "colorCategory",
          title: "tooltipTitle",
          tip: true
        }
      )
    ]
  });
}
  ```


<div style="height: 1px; background-color: #922847; margin: 1em 0;"></div>

<div style="text-align: center; font-weight: bold; font-size: 26px; margin-bottom: 20px;">
    Attack Source
</div>

<div style="width: 100%; margin-bottom: 20px; margin-top: 20px;">
Similarly, the most dangerous source of attacks for each industry was investigated.
</div>

<div class="grid grid-cols-2">
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Retail
        </div>
    ${resize((width) => attack_source_retail(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Education
        </div>
    ${resize((width) => attack_source_education(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Government
        </div>
    ${resize((width) => attack_source_government(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Telecommunications
        </div>
    ${resize((width) => attack_source_telecommunications(data_clean_withLossPerUser, {width}))}
  </div>
</div>

<div style="width: 100%; margin-bottom: 20px; margin-top: 20px;">
As with the analysis of the attack types, one attack source clearly stood out as the most harmful in terms of financial loss per affected user in each industry.
</div>

Retail: ➡️ Insiders

Education ➡️ Insiders

Government ➡️ Unknown

Telecommunications ➡️ Hacker Groups

<div style="width: 100%;">
These insights form the foundation for targeted recommendations, helping organizations in each sector focus their cybersecurity efforts on the most pressing threats they face.
</div>
