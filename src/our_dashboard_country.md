---
title: 🌎 Country Specific Analysis
toc: false
---

<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h1 style="text-align: center;">Global Cybersecurity Threats: Country Specific Analysis 🌎</h1>
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
function country_Specific_Japan(data, {width}) {
  return Plot.plot({
    marginLeft: 70,
    marginRight: 50,
    marginBottom: 60,
    marginTop: 50,
    width: 1000,
    height: 400,
    style: {
      fontWeight: "bold",
      fontSize: "18px"
    },
    x: {
      label: "Year",
      transform: String,
      tickSize: 0
    },
    y: {
      axis: "left",
      label: "↑ Number of Attacks",
      grid: true,
      domain: [0, 40],
      ticks: d3.range(0, 41, 5)
    },
    marks: [
      Plot.ruleY([0]),
      Plot.lineY(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "Japan"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        strokeWidth: 6,
        stroke: "#b33a52",
        title: "tooltip",
        tip: true
      }),
      Plot.dot(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "Japan"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        r: 6,
        fill: "#b33a52",
        title: "tooltip",
        tip: true
      })
    ]
  });
}
  ```

```js
function country_Specific_India(data, {width}) {
  return Plot.plot({
    marginLeft: 70,
    marginRight: 50,
    marginBottom: 60,
    marginTop: 50,
    width: 1000,
    height: 400,
    style: {
      fontWeight: "bold",
      fontSize: "18px"
    },
    x: {
      label: "Year",
      transform: String,
      tickSize: 0
    },
    y: {
      axis: "left",
      label: "↑ Number of Attacks",
      grid: true,
      domain: [0, 40],
      ticks: d3.range(0, 41, 5)
    },
    marks: [
      Plot.ruleY([0]),
      Plot.lineY(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "India"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        strokeWidth: 6,
        stroke: "#b33a52",
        title: "tooltip",
        tip: true
      }),
      Plot.dot(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "India"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        r: 6,
        fill: "#b33a52",
        title: "tooltip",
        tip: true
      })
    ]
  });
}
  ```

```js
function country_Specific_China(data, {width}) {
  return Plot.plot({
    marginLeft: 70,
    marginRight: 50,
    marginBottom: 60,
    marginTop: 50,
    width: 1000,
    height: 400,
    style: {
      fontWeight: "bold",
      fontSize: "18px"
    },
    x: {
      label: "Year",
      transform: String,
      tickSize: 0
    },
    y: {
      axis: "left",
      label: "↑ Number of Attacks",
      grid: true,
      domain: [0, 40],
      ticks: d3.range(0, 41, 5)
    },
    marks: [
      Plot.ruleY([0]),
      Plot.lineY(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "China"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        strokeWidth: 6,
        stroke: "#b33a52",
        title: "tooltip",
        tip: true
      }),
      Plot.dot(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "China"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        r: 6,
        fill: "#b33a52",
        title: "tooltip",
        tip: true
      })
    ]
  });
}
  ```

```js
function country_Specific_France(data, {width}) {
  return Plot.plot({
    marginLeft: 70,
    marginRight: 50,
    marginBottom: 60,
    marginTop: 50,
    width: 1000,
    height: 400,
    style: {
      fontWeight: "bold",
      fontSize: "18px"
    },
    x: {
      label: "Year",
      transform: String,
      tickSize: 0
    },
    y: {
      axis: "left",
      label: "↑ Number of Attacks",
      grid: true,
      domain: [0, 40],
      ticks: d3.range(0, 41, 5)
    },
    marks: [
      Plot.ruleY([0]),
      Plot.lineY(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "France"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        strokeWidth: 6,
        stroke: "#b33a52",
        title: "tooltip",
        tip: true
      }),
      Plot.dot(Array.from(
    d3.rollup(
      data.filter(d => d.Country === "France"),
      v => v.length,
      d => d.Year
    ),
    ([year, count]) => ({
      Year: year,
      Count: count,
      tooltip: `📅 Year: ${year}\n🚨 Attacks: ${count}`
    })
  ).sort((a, b) => a.Year - b.Year), {
        x: "Year",
        y: "Count",
        r: 6,
        fill: "#b33a52",
        title: "tooltip",
        tip: true
      })
    ]
  });
}
  ```

<div style="width: 100%; margin-bottom: 40px; margin-top: 40px;">
To gain a deeper understanding of the data and to be able to identify more meaningful patterns, we will continue to examine specific countries and observe remarkable developments and the industries mostly targeted within these countries. Having examined the data for each country, we selected the most interesting and meaningful ones to present in detail.
</div>

<div style="height: 1px; background-color: #922847; margin: 1em 0;"></div>

<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h2 style="text-align: center;">National Developments and Trends</h2>
</div>

<div class="grid grid-cols-2">
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Japan
        </div>
    ${resize((width) => country_Specific_Japan(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          India
        </div>
    ${resize((width) => country_Specific_India(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          China
        </div>
    ${resize((width) => country_Specific_China(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          France
        </div>
    ${resize((width) => country_Specific_France(data_clean_withLossPerUser, {width}))}
  </div>
</div>

<div style="width: 100%; margin-bottom: 20px; margin-top: 40px;">
Most countries, such as Japan or India, display strong volatility in attack frequencies, making it difficult to infer consistent trends. In contrast, China has shown a continuous upward trend in attack incidents since 2018, peaking at nearly 40 incidents in 2024. This pattern indicates an increasing exposure to cyber threats, underlining the need for robust preventive measures.
</div>

<div style="width: 100%; margin-bottom: 40px;">
France's pattern is more fluctuating, but the number of incidents has steadily declined since 2020. This may suggest effective mitigation strategies or a change in attackers' targets. Nevertheless, the instability between 2015 and 2020 still requires vigilance.
</div>


```js
function Industry_deepdive_UK(data, {width}) {
  return Plot.plot({
  width: 1000,
  height: 500,
  marginTop: 50,
  marginLeft: 100,
  marginBottom: 50,
  style: {
    fontWeight: "bold",
    fontSize: "16px"
  },
  x: {
    axis: "bottom",
    label: null,
    tickSize: 0
  },
  y: {
    grid: true,
    domain: [0, 4000],
    ticks: d3.range(0, 4001, 400),
    label: "↑ Avg. Loss per User (in $)"
  },
  marks: [
    Plot.ruleY([0]),
    Plot.barY(
      Array.from(
        d3.rollup(
          data.filter(d => d["Country"] === "United Kingdom"),
          v => d3.mean(v, d => d["Loss per User"]),
          d => d["Target Industry"]
        ),
        ([industry, meanLoss]) => ({
          Industry: industry,
          AvgLoss: meanLoss,
          color: meanLoss > 800 ? "#b33a52" : "#BEBEBE",
          tooltipTitle: `⚙️Industry: ${industry}\n 💸Avg. Loss/User: $${meanLoss.toFixed(2)}`
        })
      ),
      {
        x: "Industry",
        y: "AvgLoss",
        fill: "color",
        title: "tooltipTitle",
        tip: true
      }
    )
  ]
})
}
  ```

```js
function Industry_deepdive_Germany(data, {width}) {
  return Plot.plot({
  width: 1000,
  height: 500,
  marginTop: 50,
  marginLeft: 100,
  marginBottom: 50,
  style: {
    fontWeight: "bold",
    fontSize: "16px"
  },
  x: {
    axis: "bottom",
    label: null,
    tickSize: 0
  },
  y: {
    grid: true,
    domain: [0, 4000],
    ticks: d3.range(0, 4001, 400),
    label: "↑ Avg. Loss per User (in $)"
  },
  marks: [
    Plot.ruleY([0]),
    Plot.barY(
      Array.from(
        d3.rollup(
          data.filter(d => d["Country"] === "Germany"),
          v => d3.mean(v, d => d["Loss per User"]),
          d => d["Target Industry"]
        ),
        ([industry, meanLoss]) => ({
          Industry: industry,
          AvgLoss: meanLoss,
          color: meanLoss > 800 ? "#b33a52" : "#BEBEBE",
          tooltipTitle: `⚙️Industry: ${industry}\n 💸Avg. Loss/User: $${meanLoss.toFixed(2)}`
        })
      ),
      {
        x: "Industry",
        y: "AvgLoss",
        fill: "color",
        title: "tooltipTitle",
        tip: true
      }
    )
  ]
})
}
  ```

```js
function Industry_deepdive_India(data, {width}) {
  return Plot.plot({
  width: 1000,
  height: 500,
  marginTop: 50,
  marginLeft: 100,
  marginBottom: 50,
  style: {
    fontWeight: "bold",
    fontSize: "16px"
  },
  x: {
    axis: "bottom",
    label: null,
    tickSize: 0
  },
  y: {
    grid: true,
    domain: [0, 4000],
    ticks: d3.range(0, 4001, 400),
    label: "↑ Avg. Loss per User (in $)"
  },
  marks: [
    Plot.ruleY([0]),
    Plot.barY(
      Array.from(
        d3.rollup(
          data.filter(d => d["Country"] === "India"),
          v => d3.mean(v, d => d["Loss per User"]),
          d => d["Target Industry"]
        ),
        ([industry, meanLoss]) => ({
          Industry: industry,
          AvgLoss: meanLoss,
          color: meanLoss > 800 ? "#b33a52" : "#BEBEBE",
          tooltipTitle: `⚙️Industry: ${industry}\n 💸Avg. Loss/User: $${meanLoss.toFixed(2)}`
        })
      ),
      {
        x: "Industry",
        y: "AvgLoss",
        fill: "color",
        title: "tooltipTitle",
        tip: true
      }
    )
  ]
})
}
  ```


```js
function Industry_deepdive_Brazil(data, {width}) {
  return Plot.plot({
  width: 1000,
  height: 500,
  marginTop: 50,
  marginLeft: 100,
  marginBottom: 50,
  style: {
    fontWeight: "bold",
    fontSize: "16px"
  },
  x: {
    axis: "bottom",
    label: null,
    tickSize: 0
  },
  y: {
    grid: true,
    domain: [0, 4000],
    ticks: d3.range(0, 4001, 400),
    label: "↑ Avg. Loss per User (in $)"
  },
  marks: [
    Plot.ruleY([0]),
    Plot.barY(
      Array.from(
        d3.rollup(
          data.filter(d => d["Country"] === "Brazil"),
          v => d3.mean(v, d => d["Loss per User"]),
          d => d["Target Industry"]
        ),
        ([industry, meanLoss]) => ({
          Industry: industry,
          AvgLoss: meanLoss,
          color: meanLoss > 800 ? "#b33a52" : "#BEBEBE",
          tooltipTitle: `⚙️Industry: ${industry}\n 💸Avg. Loss/User: $${meanLoss.toFixed(2)}`
        })
      ),
      {
        x: "Industry",
        y: "AvgLoss",
        fill: "color",
        title: "tooltipTitle",
        tip: true
      }
    )
  ]
})
}
  ```

<div style="height: 1px; background-color: #922847; margin: 1em 0;"></div>

<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h2 style="text-align: center;">National Industry Focus</h2>
</div>

<div style="width: 100%;">
During our analysis, we discovered, that certain industries in certain countries are much more targeted than others. We have decided to display four countries in which some industries have been remarkably affected in terms of financial loss per user.
<div>

<div class="grid grid-cols-2">
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
        United Kingdom
        </div>
    ${resize((width) => Industry_deepdive_UK(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Germany
        </div>
    ${resize((width) => Industry_deepdive_Germany(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          India
        </div>
    ${resize((width) => Industry_deepdive_India(data_clean_withLossPerUser, {width}))}
  </div>
  <div class="card">
      <div style="text-align: center; font-weight: bold; margin-bottom: 20px;">
          Brazil
        </div>
    ${resize((width) => Industry_deepdive_Brazil(data_clean_withLossPerUser, {width}))}
  </div>
</div>

<div style="width: 100%;">
Further analysis reveals that in some countries, specific industries are disproportionately affected in terms of financial loss per user. The following industry-country pairs stood out:
</div>

United Kingdom ➡️ Telecommunications and Healthcare

Germany ➡️ Banking, Government and Retail

India ➡️ Telecommunications and Education

Brazil ➡️ Retail

<div style="width: 100%;">
These findings emphasize the relevance of industry-specific risk exposure and serve as a foundation for the subsequent industry-level analysis.
</div>
