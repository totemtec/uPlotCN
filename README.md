## 📈 μPlot 中文文档

一个小巧([~45 KB min](https://github.com/leeoniya/uPlot/tree/master/dist/uPlot.iife.min.js))[快速](#)的图表库，适用于时间序列、线性图、面积图、OHLC图和柱状图（MIT许可证）。

---
### Introduction

μPlot is a [fast, memory-efficient](#performance) [Canvas 2D](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)-based chart for plotting [time series](https://en.wikipedia.org/wiki/Time_series), lines, areas, ohlc & bars; from a cold start it can create an interactive chart containing 150,000 data points in 135ms, scaling linearly at [~25,000 pts/ms](https://leeoniya.github.io/uPlot/bench/uPlot-10M.html). In addition to fast initial render, the zooming and cursor performance is by far the best of any similar charting lib; at ~45 KB, it's likely the smallest and fastest time series plotter that doesn't make use of [context-limited](https://bugs.chromium.org/p/chromium/issues/detail?id=771792) WebGL shaders or WASM, both of which have much higher startup cost and code size.

<h3 align="center">166,650 point bench: <a href="https://leeoniya.github.io/uPlot/bench/uPlot.html">https://leeoniya.github.io/uPlot/bench/uPlot.html</a></h3>

However, if you need 60fps performance with massive streaming datasets, uPlot [can only get you so far](https://huww98.github.io/TimeChart/docs/performance).
If you decide to venture into this realm with uPlot, make sure to [unclog your rendering pipeline](#unclog-your-rendering-pipeline).
WebGL should still be the tool of choice for applications like realtime signal or waveform visualizations:
See [danchitnis/webgl-plot](https://github.com/danchitnis/webgl-plot), [huww98/TimeChart](https://github.com/huww98/TimeChart), [epezent/implot](https://github.com/epezent/implot), or commercial products like [LightningChart®](https://www.arction.com/lightningchart-js/).


---
### 文档 (WIP)

uPlot 文档写的不够完善。中文文档可以读 [/docs/README_CN.md](https://github.com/totemtec/uPlotCN/docs) 开始了解概念概述。完整的 API 进一步通过 /dist/uPlot.d.ts 中的注释进行了文档化。此外，越来越多的可运行 /demos 集合涵盖了 uPlot API 的绝大部分内容。

The docs are a perpetual work in progress, it seems.
Start with [/docs/README.md](https://github.com/leeoniya/uPlot/tree/master/docs) for a conceptual overview.
The full API is further documented via comments in [/dist/uPlot.d.ts](https://github.com/leeoniya/uPlot/blob/master/dist/uPlot.d.ts).
Additionally, an ever-expanding collection of runnable [/demos](https://leeoniya.github.io/uPlot/demos/index.html) covers the vast majority of uPlot's API.

---
### 第三方集成

- [Jupyter widget](https://github.com/sohailsomani/uplot_jupyter_widget) (Sohail Somani)
- [React and Vue.js](https://github.com/skalinichev/uplot-wrappers) (Sergey Kalinichev)

---