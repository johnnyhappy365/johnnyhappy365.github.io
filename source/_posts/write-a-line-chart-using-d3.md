---
title: 一步一步用 D3js 写一个 Line Chart
date: 2020-03-14 19:25:15
tags:
  - d3js
  - 数据可视化
---

[上文](write-a-bar-chart-using-d3js.md)介绍了如何用 D3js 写一个 bar chart。本文继承上文的知识点，并加以补充描述如何画一个基于时间序列为 Line Chart。
{% raw %}
<video src="/videos/linechart_demo.mov" autoplay loop="loop">
</video>
{% endraw %}

时间序列通常有几个常见的特点:

1. x 轴为时间，可能是不同的时间粒度。
2. x 轴要注意格式的显示，由于时间字符串长度较大，可能要考虑巧妙的设计 ticks 和 tickFormat。
3. 有时会用到动态实时刷新的处理
4. 通过范围选定一个时间范围，然后进行钻取(drill down)
5. 金融领域也使用时间序列，但在此基础上加了很多技术分析相关的显示组件。

以下介绍一个所有领域通用的 Line Chart.

## step by step

#### Axis

 首先先画轴。Y 轴使用的是`sacleLinear`。X 轴这里是时间序列所以要使用`scaleTime`。`scaleTime` 在缩放的时候是使用 Date 对象绽放成数值，这点需要注意。

```typescript
this.x = d3
  .scaleTime()
  .domain([data[0].x as Date, data[data.length - 1].x as Date])
  .range([margin.left, width - margin.right])
```

轴的 tick 间隔也可以基于时间进行设置

```typescript
this.xAxis = d3
  .axisBottom(this.x)
  // @ts-ignore
  .tickFormat((d: DataItem) => moment(d).format('M-D'))
  .ticks(d3.timeDay.every(2))
```

下面是显示效果：
![d3 line chart axis](/images/linechart__axis.png)

#### Series & Tooltip

画 series 时要注意，拆线图是在 svg 上是一个 line，这样就不能直接响应某个数据点的 hover 事件。所以需要在线上再加一组点，然后将 hover 的处理作用在点上。当然这个点可以设置成可见与不可见的。对于不可见的情况感觉是 hover 上线上了，实际是透明的点响应了这个事件。

```typescript
 initSeries() {
    const { data } = this.config
    const that = this

    const line = d3
      .line()
      .x((d: any, i: number) => that.x(d.x))
      .y((d: any, i: number) => that.y(d.y))
      .curve(d3.curveMonotoneX)

    this.series = this.svg
      .append('path')
      .datum(data) // 10. Binds data to the line
      .attr('class', 'line') // Assign a class for styling
      .attr('d', line)
      .attr('fill', 'none')
      .attr('stroke', Colors.primary)
      .attr('stroke-width', 3)

    const dot = this.svg
      .selectAll('.dot')
      .data(data)
      .enter()
      .append('circle') // Uses the enter().append() method
      .attr('class', 'dot') // Assign a class for styling
      .attr('cx', function(d: any) {
        return that.x(moment(d.x).toDate())
      })
      .attr('cy', function(d: any) {
        return that.y(d.y)
      })
      .attr('r', 5)
      .attr('fill', Colors.primary)
      .attr('stroke', '#fff')
      .style('cursor', 'pointer')
      .attr('stroke-width', 2)
      .on('mouseover', function(d: DataItem, i: number) {
        console.log('y', that.y(d.y))
        that.tooltip
          .transition()
          .duration(20)
          .style('opacity', 0.9)

        let left = d3.event.pageX + 20
        if (i > data.length / 2) {
          left = d3.event.pageX - 120
        }

        const maxDomainValue = d3.max(data, (d: DataItem) => d.y as number)
        let top = d3.event.pageY - 60
        if (d.y > maxDomainValue * 0.66) {
          top = d3.event.pageY + 20
        }
        that.tooltip
          .html(moment(d.x).format('YYYY-MM-DD') + '<br />' + d.y)
          .style('left', left + 'px')
          .style('top', top + 'px')
          .style('z-index', 1)
      })
      .on('mouseout', function(d: DataItem, i: number) {
        that.tooltip.style('opacity', 0).style('z-index', -1)
      })
  }
```

下面是显示效果：
![d3 line chart series](/images/linechart__series.png)

#### gridline

实现网格线的方式与[上文](write-a-bar-chart-using-d3js.md)一致，不再赘述。

## 交互

#### 实现范围选取

d3js 可以实现一个方向或两个方向的 brush 来进行范围选取。在范围选取后可以通过`d3.brushSelection(this)`来获得选区.

有几点要注意：

1. brush 层要放在 series 下，否则会出现 tooltip 不生效的现象。原来是 brush 占用了一个图层来实现 draging 的处理。有点像手机屏幕的原理，有显示层，有触摸层，有防护层。
2. 通过`brush.extent()`限制选区的范围，这里需要把 margin 去掉

```typescript
protected initBrush() {
    const that = this
    const { margin, width, height } = this.config
    const brushX = d3.brushX().extent([
      [margin.left, margin.top],
      [width - margin.right, height - margin.bottom]
    ])
    brushX
      // .on('start') // brush start event
      .on('brush', function() {
        const ext: any[] = d3.brushSelection(this)
        console.log(
          'brushing',
          ext.map((e: any) => that.x.invert(e))
        )
      })
      .on('end', function() {
        const ext: any[] = d3.brushSelection(this)
        console.log(
          'brush end',
          ext.map((e: any) => that.x.invert(e))
        )
      })
    this.svg
      .append('g')
      .attr('class', 'brush')
      .call(brushX)
  }
```

效果如下
![](/images/linechart__brush.png)
