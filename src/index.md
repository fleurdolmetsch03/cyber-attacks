---
theme: dashboard
toc: false
---

<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h1 style="text-align: center;">Global Cybersecurity Threats 🎯</h1>
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
const data_filtered = data_clean.filter(d =>
  (country_select_value === "All" || d.Country === country_select_value) &&
  (industry_select_value === "All" || d["Target Industry"] === industry_select_value)
)
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

<div class="grid grid-cols-4">
  <div class="card">
    <h2>Total Number of Attacks</h2>
    <span class="big">${data_filtered.length.toLocaleString("en-US")}</span>
  </div>
  <div class="card">
    <h2>Total Financial Loss</h2>
    <span class="big">${d3.sum(data_filtered, d => d["Financial Loss (in Million $)"]).toLocaleString("en-US", {
      maximumFractionDigits: 0
    })}M</span>
  </div>
  <div class="card">
    <h2>Number of Affected Users</h2>
    <span class="big">${(d3.sum(data_filtered, d => d["Number of Affected Users"]) / 1_000_000).toLocaleString("en-US", {
        maximumFractionDigits: 0
      })}M</span>
  </div>
  <div class="card">
    <h2>Avg. Incident Resolution Time</h2>
    <span class="big">${(d3.mean(data_filtered, d => d["Incident Resolution Time (in Hours)"])).toLocaleString("en-US", {
        maximumFractionDigits: 0
      })}</span>
  </div>
</div>


<div class="grid grid-cols-2">
    <div class="card">
      ${country_select}
    </div>
    <div class="card">
      ${industry_select}
    </div>
  </div>

```js
function dashboard_1(data, {width}) {
  return Plot.plot({
    marginBottom: 50,
    marginTop: 30,
    width,
    height: 400,
    style: {
      fontWeight: "bold",
      fontSize: "10px"
    },
    x: {
      label: "Year",
      transform: String
    },
    y: {
      axis: "left",
      label: "Financial Loss (in Million $)",
      grid: true
    },
    y2: {
      axis: "right",
      label: "Number of Attacks per Year",
      domain: [0, maxCount_attacks_filtered]
    },
    color: {
      legend: true,
      domain: ["Number of Attacks per Year", "Financial Loss (in Million $)"],
      range: ["#b33a52", "#BEBEBE"]
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(data, {
        x: "Year",
        y: "Loss",
        fill: "#BEBEBE",
        stroke: "#b33a52",
        title: "tooltipLoss",
        tip: true
      }),

      () =>
        Plot.plot({
          marginLeft: 40,
          marginRight: 20,
          marginTop: 30,
          marginBottom: 50,
          width,
          height: 400,

          marks: [
          Plot.lineY(data, {
            x: "Year",
            y: "Count",
            axis: "y2",
            stroke: "#b33a52",
            strokeWidth: 6,
            title: "tooltipCount",
            tip: true
          }),
          Plot.dot(data, {
            x: "Year",
            y: "Count",
            axis: "y2",
            r: 6,
            fill: "#b33a52",
            title: "tooltipCount",
            tip: true
          })
              ],
                x: { type: "band", axis: null },
                y: { axis: "right",
                    label: "Number of Attacks per Year",
                    domain: [0, maxCount_attacks_filtered]
                }})
      ]
  });
}
```


<!-- Plot of launch history -->

```js
function dashboard_2(data, {width} = {}) {
  return Plot.plot({
    width,
    height: 150,
    marginLeft: 50,
    marginBottom: 0,
    style: {
      fontWeight: "bold",
      fontSize: "11px"
    },
    x: {
      label: null
    },
    y: {
      label: "Avg. Incident Resolution Time (in h)",
      grid: true
    },
    marks: [
      Plot.ruleY([0]),
      Plot.barY(
        Array.from(
          d3.rollups(
            data
              .filter(d =>
                (country_select_value === "All" || d.Country === country_select_value) &&
                (industry_select_value === "All" || d["Target Industry"] === industry_select_value)
              ),
            v => d3.mean(v, d => d["Incident Resolution Time (in Hours)"]),
            d => d["Defense Mechanism Used"]
          ),
          ([defense, avgTime]) => ({
            "Defense Mechanism Used": defense,
            "Avg Resolution Time": avgTime
          })
        ).sort((a, b) => d3.ascending(a["Avg Resolution Time"], b["Avg Resolution Time"])),
        {
          x: "Defense Mechanism Used",
          y: "Avg Resolution Time",
          fill: "#BEBEBE",
          stroke: "#b33a52",
          tip: true,
          title: d =>
            `🛡️ Defense Mechanism: ${d["Defense Mechanism Used"]}\n⏱️ Avg. Resolution Time: ${d["Avg Resolution Time"].toFixed(1)}h`
        }
      )
    ]
  });
}
```




```js
function dashboard_3(data, {width} = {}) {
  return Plot.plot({
  width: 1000,
  height: 200,
  marginLeft: 170,
  marginBottom: 20,
  style: {
    fontWeight: "bold",
    fontSize: "16px"
  },
  x: {
    label: null
  },
  y: {
    label: null
  },
  color: {
    scheme: "reds",
    legend: true,
    label: "Avg. Loss per User (in $)"
  },
  marks: [
    Plot.cell(
      Array.from(
        d3.rollups(
          data
            .filter(d =>
              (country_select_value === "All" || d.Country === country_select_value) &&
              (industry_select_value === "All" || d["Target Industry"] === industry_select_value)
            ),
          v => d3.mean(v, d => d["Loss per User"]),
          d => d["Target Industry"],
          d => d["Attack Type"]
        ),
        ([industry, attackMap]) =>
          attackMap.map(([attack, avgLoss]) => ({
            "Target Industry": industry,
            "Attack Type": attack,
            "Avg Loss per User": avgLoss
          }))
      ).flat(),
      {
        x: "Attack Type",
        y: "Target Industry",
        fill: "Avg Loss per User",
        tip: true,
        title: d =>
          `🚨 Attack: ${d["Attack Type"]}\n⚙️ Industry: ${d["Target Industry"]}\n💸 Avg. Loss/User: $${d["Avg Loss per User"].toFixed(2)}`
      }
    )
  ]
})
}
```




<div class="grid grid-cols-2">
    <div class="card grid-rowspan-2">
        <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
        Number of Attacks and Financial Loss per Year
      </div>
  ${resize((width) => dashboard_1(data_clean_groupedByYear, {width}))}
    </div>
    <div class="card">
        <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
        Average Incident Resolution Time per Defense Mechanism in Hours
      </div>
  ${resize((width) => dashboard_2(data_clean_withLossPerUser, {width}))}
</div>
    <div class="card">
        <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
        Average Financial Loss per User in $
      </div>
  ${resize((width) => dashboard_3(data_clean_withLossPerUser, {width}))}
</div>
</div>
