## 📈 μPlot 中文文档

一个小巧([~45 KB min](https://github.com/leeoniya/uPlot/tree/master/dist/uPlot.iife.min.js))[快速]()的图表库，适用于时间序列、线性图、面积图、OHLC图和柱状图（MIT许可证）。

---
### 介绍

μPlot 是一个基于 [Canvas 2D](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D) 的[快速、内存高效]()的图表库，适用于绘制[时间序列](https://en.wikipedia.org/wiki/Time_series)、线性图、面积图、OHLC 图和柱状图。从冷启动开始，它可以在 135 毫秒内创建一个包含 150,000 个数据点的交互式图表，每秒线性缩放约为 [~25,000 pts/ms] 个数据点。除了快速的初始渲染外，缩放和光标性能远远优于任何类似的图表库；在约 45 KB 的代码大小下，它很可能是最小、最快的时间序列绘图库，而且不使用受[上下文限制](https://bugs.chromium.org/p/chromium/issues/detail?id=771792)的 WebGL 着色器或 WASM，这两者的启动成本和代码大小都要高得多。

<h3 align="center">166,650 点的性能测试数据: <a href="https://leeoniya.github.io/uPlot/bench/uPlot.html">https://leeoniya.github.io/uPlot/bench/uPlot.html</a></h3>

然而，如果你需要在大规模的流数据集上实现 60 帧每秒的性能，uPlot 可能[无法满足你的需求](https://huww98.github.io/TimeChart/docs/performance)。如果你决定使用 uPlot 进入这个领域，请确保[清除你的渲染管道]()。对于实时信号或波形可视化等应用程序，WebGL 仍然是首选工具：可以参考 [danchitnis/webgl-plot](https://github.com/danchitnis/webgl-plot)， [huww98/TimeChart](https://github.com/huww98/TimeChart)， [epezent/implot](https://github.com/epezent/implot)， 或者商业产品，比如 [LightningChart®](https://www.arction.com/lightningchart-js/)。

---
### 文档 (WIP)

uPlot 文档写的不够完善。学习的话可以先读中文文档 [/docs/README_CN.md](hhttps://github.com/totemtec/uPlotCN/blob/main/docs/README_CN.md) 了解概念。然后，跑跑官方 [/demos](https://leeoniya.github.io/uPlot/demos/index.html) 。这些例子涵盖了 uPlot API 的绝大部分内容。如果要查阅完整的 API 可以查看代码的中文注释 [/dist/uPlot.d.ts](https://github.com/totemtec/uPlotCN/blob/main/dist/uPlot.d.ts) 。


---
### 第三方集成

- [Jupyter widget](https://github.com/sohailsomani/uplot_jupyter_widget) (Sohail Somani)
- [React and Vue.js](https://github.com/skalinichev/uplot-wrappers) (Sergey Kalinichev)

---