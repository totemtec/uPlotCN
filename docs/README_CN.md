### Documentation

- [安装](#installation)
- [数据格式](#data-format)
- [基础用法](#basics)
- [高低带](#highlow-bands)
- [序列，刻度尺，轴，网格](#series-scales-axes-grid)
- [多轴和多刻度尺](#multiple-scales--axes)
- [刻度尺配置](#scale-opts)
- [轴和网格配置](#axis--grid-opts)
- WIP: [#48](https://github.com/leeoniya/uPlot/issues/48)

---
#### 安装

```html
<link rel="stylesheet" href="dist/uPlot.min.css">
<script src="dist/uPlot.iife.min.js"></script>
```

---
#### 数据格式

```js
let data = [
  [1546300800, 1546387200],    // x-values，x轴数据数组 (时间戳，秒，可用浮点数)
  [        35,         71],    // y-values，y轴数据数组1 (第1序列)
  [        90,         15],    // y-values，y轴数据数组2 (第2序列)
];
```

uPlot 需要如上所示的2维数组数据格式。

- x-values 值必须是数字、唯一且按升序排列。
- y-values 值必须是数字（或用 null 表示缺失数据）。
- x-values 和 y-values 数组长度必须大于等于2。

默认情况下，x 值被假定为 [unix 时间戳](https://en.wikipedia.org/wiki/Unix_time) （自 1970-01-01 00:00:00 以来的秒数）但可以通过 `scales.x.time = false` 指定为纯数字。
JavaScript 使用毫秒精度的时间戳，`time: true` 强制使用毫秒精度的时间戳？？？。对于亚秒级时间段，建议设置 `time: false`，并简单地使用从0开始的毫秒偏移量。如果您真的需要考虑日历的毫秒级精度，则可以将时间戳提供为浮点数，例如`1575354886.419`。
[更多信息](https://github.com/leeoniya/uPlot/issues/60#issuecomment-561158077).

对于无法沿 x 轴进行对齐的多系列数据集来说，uPlot 可能不是一个理想的选择。如果其中一个系列数据密集，而另一个系列数据稀疏，则后者将需要使用大量的`null` 值进行填充。如果每个系列都具有任意的 x 值数据，则需要使用所有 x 值来扩充 x 值数组，并使用`null`来扩充所有 y 值数组，这可能导致数据集大小呈指数增长，并且结构主要由`null`组成。

这并不意味着所有系列必须具有相同的 x 值 - 只需要它们可以对齐即可。
例如，可以绘制[不同时间段](https://leeoniya.github.io/uPlot/demos/time-periods.html)，因为数据是等间隔的。


**在选择 uPlot 之前，请确保您的数据符合这些要求。**

---
#### 基础用法

```js
let opts = {
  title: "My Chart",
  id: "chart1",
  class: "my-chart",
  width: 800,
  height: 600,
  series: [
    {},
    {
      // initial toggled state (optional)
      show: true,

      spanGaps: false,

      // in-legend display
      label: "RAM",
      value: (self, rawValue) => "$" + rawValue.toFixed(2),

      // series style
      stroke: "red",
      width: 1,
      fill: "rgba(255, 0, 0, 0.3)",
      dash: [10, 5],
    }
  ],
};

let uplot = new uPlot(opts, data, document.body);
```

- `id` 和 `class` 是设置图表容器`<div>`的可选属性 (`uplot.root`).
- `width` 和 `height` 是绘图区域的尺寸，包含轴和刻度，但 **不包含** `title` 和 `legend` 的尺寸，这两个可以通过 CSS 来设置。
- `spanGaps` 设为 `true` 可以连接为 `null` 的数据点.
- 要渲染一个序列，就 **必须** 在 opts 中指定；只包含在 data 中是不行的。
- 所有序列的 options 都是可选的； `label` 默认为 "Value" ，`stroke` 默认为 "black".
- `width` 是序列线宽，单位是 css 像素。
- `stroke`， `width`， `fill` 和 `dash` 直接用于 Canvas API 的 [ctx.strokeStyle](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/strokeStyle), [ctx.lineWidth](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineWidth), [ctx.fillStyle](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillStyle), and [ctx.setLineDash](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/setLineDash).

---
#### 高低带（频带/波段）

高/低带是由两个相邻的 `data` 系列按低、高顺序定义的。并且需要指定 `series.band = true`。

```js
const opts = {
  series: [
    {},
    {
      label: "Low",
      fill: "rgba(0, 255, 0, .2)",
      band: true,

    },
    {
      label: "High",
      fill: "rgba(0, 255, 0, .2)",
      band: true,
    },
  ],
};
```

---
#### 序列，刻度尺，轴，网格

uPlot 的 API 力求简洁、一致性和逻辑一致性。理解了 `data`（数据）， `series`（序列）， `scales`（刻度尺）和 `axes`（轴）的角色和处理顺序将有助于理解其余主题。
高级渲染流程如下：

1. `data` 第一个输入系统中的。
0. `series` 包含每个数据集的配置，例如可见性、样式、图例中的标签和值显示，以及它们应该沿着哪个 `scale` 键绘制。`data[0]` 的隐式刻度尺键为x，`data[1..N]` 的隐式刻度尺键为y。

0. `scales` 反映在视图中可见的最小/最大范围。所有视图范围调整（如缩放和分页）都在这里完成。如果没有通过 opts 显式设置，则刻度尺会使用系列配置自动初始化，并使用提供的数据自动调整范围。

0. `axes` 沿着它们的刻度尺呈现刻度、值、标签和网格。刻度和网格间距、值的粒度和格式化、时区和夏令时（DST）处理都在这里完成。

你可能注意到上面例子中 `series` 和 `axes` 数组都以 `{}` 开始，它表示的是 `x` 轴方向的序列和轴。

由于uPlot设置默认值的方式，它们是必需的：

- `data[0]`， `series[0]` 和 `axes[0]` 表示和继承 `x` 默认值，例如：

  - `"x"` scale w/ `auto: false`
  - 时序的
  - 水平方向，位于底部
  - 更大的最小刻度间距

- `data[1..N]`， `series[1..N]` 和 `axes[1..N]` 表示和继承 `y` 的默认值，例如：

  - `"y"` scale w/ `auto: true`
  - 数字的
  - 垂直方向，靠左定位
  - 较小的最小刻度间距

虽然有些不寻常，但将 x 和 y opts 保留在扁平数组中 [而不是将它们分开] 具有几个目的:

- API 和结构上的统一性。例如 `series[i]` 对应于 `data[i]`
- Hooks 在不需要进一步上下文的情况下，可以接收到数组的索引 `i`
- 内部结构不需要额外的复杂性来隐藏所有内容已经合并且DRY的事实？？？

更多思考在 [#76](https://github.com/leeoniya/uPlot/pull/76) 和 [#77](https://github.com/leeoniya/uPlot/issues/77).

---
#### 多轴和多刻度尺

具有不同单位的系列可以沿着额外的刻度尺绘制并显示相应的 y 轴。

1. 使用相同的 `series.scale` 键。
2. 可以用 `scale` 键，指定额外的 `axis` 。

```js
let opts = {
  series: [
    {},
    {
      label: "CPU",
      stroke: "red",
      scale: "%",
      value: (self, rawValue) => rawValue.toFixed(1) + "%",
    }
    {
      label: "RAM",
      stroke: "blue",
      scale: "%",
      value: (self, rawValue) => rawValue.toFixed(1) + "%",
    },
    {
      label: "TCP",
      stroke: "green",
      scale: "mb",
      value: (self, rawValue) => rawValue.toFixed(2) + "MB",
    },
  ],
  axes: [
    {},
    {
      scale: "%",
      values: (self, ticks) => ticks.map(rawValue => rawValue.toFixed(1) + "%"),
    },
    {
      scale: "mb",
      values: (self, ticks) => ticks.map(rawValue => rawValue.toFixed(2) + "MB"),
      side: 1,
      grid: {show: false},
    },
  ],
};
```

- `side` 表示轴位置（0: 顶部， 1: 右侧， 2: 底部， 3: 左侧）。


#### 转换单位的轴

提供个附加轴显示转换单位，这个功能有时候很有用，例如 °F 或 °C。这要使用刻度尺依赖来完成。

```js
let opts = {
  series: [
    {},
    {
      label: "Temp",
      stroke: "red",
      scale: "F",
    },
  ],
  axes: [
    {},
    {
      scale: "F",
      values: (self, ticks) => ticks.map(rawValue => rawValue + "° F"),
    },
    {
      scale: "C",
      values: (self, ticks) => ticks.map(rawValue => rawValue + "° C"),
      side: 1,
      grid: {show: false},
    }
  ],
  scales: {
    "C": {
      from: "F",
      range: (self, fromMin, fromMax) => [
        (fromMin - 32) * 5/9,
        (fromMax - 32) * 5/9,
      ],
    }
  },
```

- `from` 指定依赖的刻度尺。
- `range` 从 `from` 转换最大最小值。

---
#### 刻度尺选项

如果刻度尺不需要从可见数据来调整范围，你可以提供静态的最大最小值，这也是一项性能优化。

```js
let opts = {
  scales: {
    "%": {
      auto: false,
      range: [0, 100],
    }
  },
}
```

默认 x 轴是时序性的，单也可以切换为纯数字。
The default x scale is temporal, but can be switched to plain numbers. 这可用于绘制函数。？？？

```js
let opts = {
  scales: {
    "x": {
      time: false,
    }
  },
}
```

刻度的默认分布是线性的 `distr: 1`，但可以切换到指数或等间距的。当你想要压缩没有数据的时间段，例如周末时，这是很有用的。请记住，这将禁用逻辑时间刻度基线，例如一天的开始或一个月的开始。

```js
let opts = {
  scales: {
    "x": {
      distr: 2,
    }
  },
}
```

---
#### 轴和网格选项

大多数选项都是不言自明的：

```js
let opts = {
  axes: [
    {},
    {
      show: true,
      label: "Population",
      labelSize: 30,
      labelFont: "bold 12px Arial",
      font: "12px Arial",
      gap: 5,
      size: 50,
      stroke: "red",
      grid: {
        show: true,
        stroke: "#eee",
        width: 2,
        dash: [],
      },
      ticks: {
        show: true,
        stroke: "#eee",
        width: 2,
        dash: [],
        size: 10,
      }
    }
  ]
}
```

- `size` 和 `labelSize` 分别指定 `values` 和 `labels` DOM 元素的在垂线方向的尺寸。在上面的例子中，这个 y 轴的完整宽度为 30 + 50；对于一个 x 轴来说，这是它的高度。
- `gap` 是轴刻度和 `values` 之间的间距。

自定义刻度/网格间距、数值格式化和间隔尺寸需要更深入的了解：

```js
let opts = {
  axes: [
    {
      space: 40,
      incrs: [
         // minute divisors (# of secs)
         1,
         5,
         10,
         15,
         30,
         // hour divisors
         60,
         60 * 5,
         60 * 10,
         60 * 15,
         60 * 30,
         // day divisors
         3600,
      // ...
      ],
      // [0]:   minimum num secs in found axis split (tick incr)
      // [1]:   default tick format
      // [2-7]: rollover tick formats
      // [8]:   mode: 0: replace [1] -> [2-7], 1: concat [1] + [2-7]
      values: [
      // tick incr          default           year                             month    day                        hour     min                sec       mode
        [3600 * 24 * 365,   "{YYYY}",         null,                            null,    null,                      null,    null,              null,        1],
        [3600 * 24 * 28,    "{MMM}",          "\n{YYYY}",                      null,    null,                      null,    null,              null,        1],
        [3600 * 24,         "{M}/{D}",        "\n{YYYY}",                      null,    null,                      null,    null,              null,        1],
        [3600,              "{h}{aa}",        "\n{M}/{D}/{YY}",                null,    "\n{M}/{D}",               null,    null,              null,        1],
        [60,                "{h}:{mm}{aa}",   "\n{M}/{D}/{YY}",                null,    "\n{M}/{D}",               null,    null,              null,        1],
        [1,                 ":{ss}",          "\n{M}/{D}/{YY} {h}:{mm}{aa}",   null,    "\n{M}/{D} {h}:{mm}{aa}",  null,    "\n{h}:{mm}{aa}",  null,        1],
        [0.001,             ":{ss}.{fff}",    "\n{M}/{D}/{YY} {h}:{mm}{aa}",   null,    "\n{M}/{D} {h}:{mm}{aa}",  null,    "\n{h}:{mm}{aa}",  null,        1],
      ],
  //  splits:
    }
  ],
}
```

- `space` 是相邻刻度之间的最小间距；较小的数字就是用较小的分割器。也可以是一个函数，形式为 `(self, axisIdx, scaleMin, scaleMax, dim) => space` ，其中 dim 是沿轴的维度在 CSS 像素中的尺寸。
- `incrs` 是给轴分段，生成刻度的分割器。也可以是形式为 `(self) => divisors` 的函数。
- `values` 可以是：
  - 具有形式 `(self, ticks, space) => values` 的函数，其中 ticks 是沿轴比例尺的原始值数组，space 是以 CSS 像素确定的刻度间距，values 是格式化的刻度标签数组。
  - 带断点的刻度格式化方法数组。
