---
title: 时间序列图的所有秘籍
date: 2020-03-19 20:51:38
tags:
  - d3js
  - 数据可视化
  - 时间序列
---

时间序列在数据可视化中比较常见，通过是表达随时间的变化(x 轴)指标(y 轴)是如何变化的。本文介绍一些在表示时间序列的时候的一些知识点和技巧，在最后给出一个 cheatsheet 可以快速查阅。

## 二维

时间序列通常是一个二维的图表，X 轴用来表示时间轴，Y 轴用来表示指标轴。比如在
{% post_link write-a-line-chart-using-d3 '一步一步用 D3js 写一个 Line Char' %}介绍的 line chart

![line chart](/images/linechart__series.png)

#### x 轴

通过使用线性的时间缩放，在 d3js 中可以使用 `scaleTime`来实现。时间显示的粒度通常要根据具体业务来确定，有的可以看年统计，也有的按小时统计。

另外，在显示 x 轴的 tick 的时候，因为日期的不同粒度以及不同的时间显示精度，经常是需要把 x 轴的时间做格式化的。如按天为粒度的时间序列图可以显示格式为`MM-DD`。

#### y 轴

y 轴通常用来表示指标，它可能是一个具体的数据记录也可能是聚合后的数据。有时，也会使用双 y 轴的情况，这时会显示两个 Series 分别用不同的 y 轴来解释。这种场景是为了强调二个指标随着同一段时间变化时的相关关系。

## Series

#### 离散数据与连续数据

离散数据是经常聚合后的数据，如每小时/天的 sum/avg 值等。

## Series 的表示形式

下面说说数据序列，基于时间序列的指标展示有多种方式，每种方式有其特殊的适用场景。

#### column chart 柱形图/直方图

column chart 通常是用来显示聚合后的离散数据的。使用柱图可以强化柱之间的比较效果。

![column chart](/images/columnchart__series.png)

#### scatter chart/散点图

散点图最大的特点我觉得有二个，一是两个轴都是连续型的或数据型的。二是用小圆点来表示一个数据点。在这个二维平面上，散点图的最大的意义大于提示二个轴所代表的指标的几何意义。比如下图，两个指标就是就代表了两个指标是`正相关`的。
![scatter chart](/images/scatterchart__series.png)
除了`正相关`散点图还可以表达`不相关`/`聚焦`/`负相关`等含义

#### line chart/折线图

把 scatter chart 中的点用直线连接在一起就变成了 line chart。这种连接会在视觉上让用户更倾向于 y 轴数据的变化关系，而不再纠结与 x 与 y 轴的关系。

在 line chart 默认是使用直线的，有些图表库要支持使用 spline line 的方式来拟合。这种拟合其实是与实际的指标变化无关的，我觉得可能更多的是从美观度上的考虑。

#### step chart/阶梯图

有时指标在一个不能在图表上再分的时间段内是固定不变的。如银行利率在很长一段时间是不会改变的。为了强调这种`不变`的关系。用 step chart 就非常适合。

step chart 在画 series 时也是使用`d3.line`进行绘制然后生成一个 path。区别是，在原有的数据点之外还要再补充一些点才能画出这种直角的感觉。
参考代码如下：

```typescript
initSeries() {
    const { data } = this.config
    const that = this

    const stepData: DataItem[] = []
    data.forEach((d: DataItem, i: number) => {
      stepData.push(d)
      if (i !== data.length - 1) {
        stepData.push({
          x: data[i + 1].x,
          y: d.y
        })
      }
    })

    const line = d3
      .line()
      .x((d: any, i: number) => that.x(d.x))
      .y((d: any, i: number) => that.y(d.y))

    this.series = this.svg
      .append('path')
      .datum(stepData) // 10. Binds data to the line
      .attr('class', 'line') // Assign a class for styling
      .attr('d', line)
      .attr('fill', 'none')
      .attr('stroke', Colors.primary)
      .attr('stroke-width', 3)
  }
```

`d3js`提供了这种便利性，可以通过设置 curve 来实现, 效果是一样的。

```typescript
const line = d3
  .line()
  .x((d: any, i: number) => that.x(d.x))
  .y((d: any, i: number) => that.y(d.y))
  .curve(d3.curveStep)
```

![step chart](/images/stepchart__series.png)

## cheatsheet

在时间序列的展示形式选择的时候可以参考这个 cheatsheet。

|                              | column | scatter | line | step   |
| ---------------------------- | ------ | ------- | ---- | ------ |
| 离散数据                     | 最适合 | 适合    |      |        |
| 连续数据                     |        | 适合    | 适合 | 适合   |
| 时间段内数据不变             |        |         |      | 最适合 |
| 强调指标之间的对比           | 最适合 |
| 强调时间与指标指间的几何关系 |        | 最适合  | 适合 |
