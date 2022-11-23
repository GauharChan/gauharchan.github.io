<Banner />
# antv/x6

> 用于实现流程图，可进行交互。本文记录的是`vue`的用法。

[完整的 demo](https://codesandbox.io/s/x6-demo-ffwso)

<iframe
  src="https://codesandbox.io/embed/x6-demo-ffwso?codemirror=1"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

## 安装

> 建议使用`yarn`安装哈，一开始我也是贪`cnpm`速度快一点，结果安装不了`x6-vue-shape`

```sh
# yarn
$ yarn add @antv/x6

# yarn
yarn add @antv/x6-vue-shape

# 在 vue2 下还需要安装 @vue/composition-api
yarn add @vue/composition-api --dev
```

## 1.创建画布

- `keyboard`开启监听键盘
- `selecting`设置选择节点，可通过`filter`去过滤。我只需要选择连接线(x6 中叫边`Edge`)。`graph.isEdge(node)`判断传入的节点是否为边(Edge)
- `scroller`使用`scroller`模式，超出`container`会出现滚动条可移动查看节点`node`,`pannable`是否启用画布平移能力
- `translating`配置移动选项，我这里限制了子组件不可以拖动，限制在父组件里面，只能移动父组件

```js
import "@antv/x6-vue-shape";
import { Graph, Line, Path, Curve, Addon } from "@antv/x6";

export default {
  data() {
    return {
      graph: null,
    };
  },
};
// 创建画布
this.graph = new Graph({
  container: document.getElementById("drag-container"),
  keyboard: true,
  scroller: {
    enabled: true,
    pannable: true,
  },
  selecting: {
    enabled: true,
    rubberband: true,
    filter(node) {
      // 只选连接线(边)
      return that.graph.isEdge(node);
    },
  },
  translating: {
    restrict(view) {
      const cell = view.cell;
      if (cell.isNode()) {
        const parent = cell.getParent();
        if (parent) {
          // 限制子节点不能移动
          return {
            x: cell.getBBox().x,
            y: cell.getBBox().y,
            width: 0,
            height: 0,
          };
        }
      }
      return null;
    },
  },
});
```

## 2.注册节点

我的节点都是用 vue 组件写的，并不是用原生 svg 的一些图形(circle、rect)，实际开发的需求肯定不是这么简单，vue 组件必须用`registerVueComponent`注册，否则后面做回显时，使用不了`toJSON`和`fromJSON`。回显时使用这两个 api，不需要任何的操作，渲染即可

```js
import Drag from "@/components/drag/drag.vue";
import Item from "@/components/drag/item.vue";

const that = this;
// 注册父组件
Graph.registerVueComponent(
  "Drag",
  {
    template: '<drag @parentnoderemove="parentnoderemove"></drag>',
    methods: {
      parentnoderemove({ id }) {
        // 删除对应的节点
        that.saveNodes.splice(
          that.saveNodes.findIndex((item) => item.id === id),
          1
        );
      },
    },
    components: {
      Drag,
    },
  },
  true
);
// 注册子组件
Graph.registerVueComponent(
  "Item",
  {
    template: '<item @edge="edge"></item>',
    methods: {
      // 两个节点之间连线
      edge: that.edge,
    },
    components: {
      Item,
    },
  },
  true
);
```

## 3.生成节点

我这里用到了拖拽生成节点，对于拖拽，`x6`提供了`Dnd`。首先对`Dnd`进行创建配置

`getDropNode`配置拖拽结束，创建节点之前执行的函数，这个函数一定要`return node`节点

```js
// 拖拽
this.dnd = new Addon.Dnd({
  target: this.graph, // 画布对象
  scaled: false,
  animation: true,
  getDropNode: that.handleEndDrag,
});
```

- 对开始拖拽的目标节点`<div>`添加`mousedown`事件去执行`Dnd.start`事件即可，start 函数要传入`mousedown`事件的`event`对象

```vue
<template>
  <div>
    <div class="side-title">• 统计对象</div>
    <div
      class="side-item"
      v-for="(item, index) in leftSide"
      :key="index"
      @mousedown="handleDrag(item, $event)"
    >
      {{ item.title }}
    </div>
  </div>
</template>
```

methods

创建节点需要提交 x、y 坐标值，所以在 handleEndDrag 事件中我先让返回的父组件渲染完成后，获取父组件的 x、y 的值，再创建子节点。

```vue
<script>
export default {
  methods: {
    // 开始拖拽
    handleDrag(item, e, weidu) {
      // 业务逻辑

      // item请求。。
      this.createChildren = [1, 2, 3]; // item请求回来的数据
      this.handleCreateNode(item, e);
    },
    handleCreateNode(item, e) {
      const that = this;
      // 创建父节点
      const parent = this.graph.createNode({
        shape: "vue-shape",
        x: 100,
        y: 100,
        height: that.createChildren.length * 60 + 58, // 根据实际的UI样式来
        data: {
          item,
          height: that.createChildren.length * 60 + 58,
        },
        component: "Drag", // 这个名字对应registerVueComponent的名字
      });
      // 开始拖拽
      this.dnd.start(parent, e);
    },
    // 拖拽结束，渲染节点之前，必须返回克隆的节点
    handleEndDrag(node) {
      const cloneNode = node.clone({ deep: true });
      const that = this;
      // 父节点渲染之后再执行，因为需要父节点的位置
      this.$nextTick(() => {
        const { x, y } = cloneNode.position();
        // 是否第一个节点
        const cellCount = that.graph.getCellCount();
        this.createChildren.forEach((item, index) => {
          const child = this.graph.addNode({
            shape: "vue-shape",
            x: x + 20,
            y: index === 0 ? y + 58 : y + (index * 60 + 58),
            width: 240,
            height: 46,
            data: {
              item,
            },
            component: "Item",
          });
          cloneNode.addChild(child); // 添加到父节点
        });
        this.saveNodes.push(cloneNode);
        if (cellCount === 1) {
          this.firstNode = cloneNode;
          that.$message.warning("第一个统计对象为主体");
        }
      });
      return cloneNode;
    },
  },
};
</script>
```

## 4.两个节点之间连线(创建边)

需求是点击两个 node，就让它们连线

点击的事件我放在了 item 组件里，点击后触发父组件这边的 edge 方法

`connector`决定你的线是怎样的，我这边是圆弧

```js
// 连线规则，圆弧
Graph.registerConnector(
  // Graph不是创建的画布实例(this.graph)!!!
  "smooth",
  (sourcePoint, targetPoint, routePoints, options) => {
    const line = new Line(sourcePoint, targetPoint);
    const diff = 5;
    const factor = 1;
    const vertice = line
      .pointAtLength(line.length() / 2 + 12 * factor * Math.ceil(diff))
      .rotate(90, line.getCenter());

    const points = [sourcePoint, vertice, targetPoint];
    const curves = Curve.throughPoints(points);
    const path = new Path(curves);
    return options.raw ? path : path.serialize();
  },
  true
);
```

```js
// 两个node之间连线
edge (node) {
  // console.log('node', node.id)
  // console.log('AllEdges', this.graph.getEdges(node))
  const allEdges = this.graph.getEdges(node)
  this.waitEdgeNodes.push(node)
  if (this.waitEdgeNodes.length === 2) {
    // 改变active状态
    this.$store.dispatch('callNodes', this.waitEdgeNodes.map(item => item.id))
    // 判断不是同一父级
    if (this.waitEdgeNodes[0]._parent.id !== this.waitEdgeNodes[1]._parent.id) {
      // 要连线的目标id和来源id
      const allTargetAndAllSource = allEdges.map((item) => [
        item.getTargetCellId(),
        item.getSourceCellId()
      ])
      const flag = allTargetAndAllSource.filter(item =>
        item.includes(this.waitEdgeNodes[0].id) && item.includes(this.waitEdgeNodes[1].id
      ))
      // 如果两个点已经连过线,
      if (flag.length) return (this.waitEdgeNodes.length = 0)
      // 这里通过坐标决定连线的点
      const sourceAnchor = this.waitEdgeNodes[0].getBBox().x < this.waitEdgeNodes[1].getBBox().x ? 'right' : 'left'
      const targetAnchor = this.waitEdgeNodes[0].getBBox().x < this.waitEdgeNodes[1].getBBox().x ? 'left' : 'right'
      // 设置箭头的大小
      const args = {
        size: 8
      }
      this.graph.addEdge({
        source: { cell: this.waitEdgeNodes[0], anchor: sourceAnchor, connectionPoint: 'anchor' },
        target: { cell: this.waitEdgeNodes[1], anchor: targetAnchor, connectionPoint: 'anchor' },
        connector: { name: 'smooth' },
        attrs: {
          line: {
            strokeDasharray: '5 5',
            stroke: '#666',
            strokeWidth: 1,
            sourceMarker: {
              args,
              name: 'block' // 实心箭头
            },
            targetMarker: {
              args,
              name: 'block'
            }
          }
        }
      })
    }
    // 无论如何都清空
    this.waitEdgeNodes.length = 0
  }
},
```

## 5.选择连接线，并监听键盘删除键进行删除

因为一开始的配置就限制了只能选择 edge，所以这里不用判断其他的 cell

这部分主要是更改样式

```js
// 选择连接线(边)事件
this.graph.on("selection:changed", ({ added, removed }) => {
  this.selectLine = added;
  added.forEach((cell) => {
    const args = { size: 15 };
    cell.setAttrs({
      line: {
        sourceMarker: {
          args,
          name: "block",
        },
        targetMarker: {
          args,
          name: "block",
        },
        stroke: "#2D8CF0",
        strokeWidth: 4,
      },
    });
  });
  removed.forEach((cell) => {
    const args = { size: 8 };
    cell.setAttrs({
      line: {
        sourceMarker: {
          args,
          name: "block",
        },
        targetMarker: {
          args,
          name: "block",
        },
        stroke: "#666",
        strokeWidth: 1,
      },
    });
  });
});
```

**监听删除键删除**

通过 cell.remove 方法删除

```js
// 删除连接线(边)
this.graph.bindKey(["Backspace", "Delete"], (e) => {
  if (this.selectLine.length) {
    this.$confirm("确认删除连线吗?", "提示", {
      confirmButtonText: "确定",
      cancelButtonText: "取消",
      type: "warning",
    }).then(() => {
      this.selectLine[0].remove();
      this.$message({
        type: "success",
        message: "删除成功!",
      });
    });
  }
});
```

# L7

> 地理空间数据可视化(**地图)**

## cdn

```html
<script src="https://cdn.jsdelivr.net/npm/@antv/l7@2.2.40/dist/l7.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@antv/l7-district@2.2.39/dist/l7-district.min.js"></script>
```

在`shims-tsx.d.ts`文件中声明 L7 对象

```ts
import Vue, { VNode } from "vue";

declare global {
  interface Window {
    combineHeader: any;
  }
  interface Icol {
    span: number;
    offset?: number;
  }
  let L7: any; // L7地图
}
```

## 创建 Scene 场景

```ts
const { Scene, District, LineLayer, Popup } = L7;

const scene = new Scene({
  id: "map", // 渲染容器
  logoVisible: false, // 是否展示logo
  map: new GaodeMap({
    // 使用高德地图
    pitch: 0, // 倾斜角度
    style: "blank", // dark light normal blank(无底图)
    zoom: 4, // 默认缩放
    minZoom: 4, // 最小缩放
    maxZoom: 4, // 最大缩放
  }),
});
```

## 基础地图

## 创建中国地图

```ts
// 中国地图
const country = new District.CountryLayer(scene, {
  data: this.provinceData, // 地图数据(各省的数据)
  joinBy: ["NAME_CHN", "name"], // 数据关联，L7默认是拿NAME_CHN这个字段渲染数据。而我们this.provinceData(数据)的省份字段名为name。省份名字对应才能拿到数据
  depth: 1, // 0：国家级，1:省级，2: 市级，3：县级
  label: {
    color: "green", // 省份名字的字体颜色
  },
  provinceStroke: "#783D2D", // 省界颜色
  cityStroke: "#EBCCB4", // 城市边界颜色
  cityStrokeWidth: 1, // 城市边界宽度
  fill: {
    // 每个省份板块的填充
    color: {
      field: "value", // this.provinceData(数据)中的字段名。就是下面val的值
      // 根据provinceData的value值，显示不同的颜色
      values: (val: number) => {
        if (val > 20000) {
          return "#e6550d";
        } else if (val === 1477.63) {
          return "#a63603";
        } else {
          return "#fdae6b";
        }
      },
    },
    activeColor: "#bfa", // 鼠标滑过颜色
  },
  popup: {
    // 弹窗，触发事件默认鼠标滑过
    enable: true,
    Html: (props: any) => {
      return `<span>${props.NAME_CHN}: ${props.value}</span>`;
    },
  },
});
```

## 钻地地图

## 中国地图两地连线

```js
async initChinaChart () {
  const res = await this.$api.userStatisticsQueryUserDistributionFetch(this.getSearchForm)
  this.provinceData = res.data
  const { Scene, District, LineLayer, Popup } = L7;

  const scene = new Scene({
    id: "map", // 渲染容器
    logoVisible: false, // 是否展示logo
    map: new GaodeMap({ // 使用高德地图
      pitch: 0, // 倾斜角度
      style: "blank", // dark light normal blank(无底图)
      zoom: 4, // 默认缩放
      minZoom: 4, // 最小缩放
      maxZoom: 4 // 最大缩放
    })
  });
  scene.on("loaded", () => {
    // 线
    const layer = new LineLayer({
      zIndex: 10, // 图层绘制顺序，数值大绘制在上层，可以控制图层绘制的上下层级。不设置会被地图遮住
      blend: "normal",
      popup: {
        enable: true,
        Html: (props: any) => {
          return `<span>${props.NAME_CHN}: ${props.value}</span>`;
        }
      }
    })
    .source(this.jsonData, {
      parser: {
        type: "json",
        x: "lng1",
        y: "lat1",
        x1: "lng2",
        y1: "lat2"
      }
    })
    .size(3)
    .active({
      color: "red"
    })
    .shape("arc")
    .color("blue")
    .style({
      opacity: 0.8,
      blur: 0.99
    })
    // .animate({
    //   interval: 0.2,
    //   trailLength: 1,
    //   duration: 1
    // });
    // 鼠标移动到线的提示框
    const popup: any = new Popup({
      offset: [0, 0],
      closeButton: false
    });
    layer.on("mousemove", (e: any) => {
      // 设置 popup 的经纬度位置
      popup
        .setLnglat(e.lngLat)
        .setHTML(`<div>${e.feature.from} - ${e.feature.to}</div>`);
      scene.addPopup(popup);
    });
    layer.on("mouseout", () => {
      popup.close();
    });
    scene.addLayer(layer);
    // 中国地图
    const country = new District.CountryLayer(scene, {
      data: this.provinceData, // 地图数据(各省的数据)
      joinBy: ["NAME_CHN", "province_name"], // 数据关联，L7默认是拿NAME_CHN这个字段渲染数据。而我们this.provinceData(数据)的省份字段名为name。省份名字对应才能拿到数据
      depth: 1, // 0：国家级，1:省级，2: 市级，3：县级
      label: {
        color: "green" // 省份名字的字体颜色
      },
      provinceStroke: "#783D2D", // 省界颜色
      fill: { // 每个省份板块的填充
        color: {
          field: "num", // this.provinceData(数据)中的字段名。就是下面val的值
          // 根据provinceData的value值，显示不同的颜色
          values: (val: number) => {
            if (val > 3) {
              return "#e6550d";
            } else if (val === 3) {
              return "#a63603";
            } else {
              return "#fdae6b";
            }
          }
        },
        activeColor: "#bfa" // 鼠标滑过颜色
      },
      popup: { // 弹窗，触发事件默认鼠标滑过
        enable: true,
        Html: (props: any) => {
          return `<span>${props.NAME_CHN}: ${props.num}</span>`;
        }
      }
    });
  });
}
```

## 双曲线

```js
initTestChart () {
  const data: any[] = [
    {
      key: "🍎",
      value: 2,
      type: "全家"
    },
    {
      key: "🍎",
      value: 10,
      type: "美宜佳"
    },
    {
      key: "🍌",
      value: 5,
      type: "全家"
    },
    {
      key: "🍌",
      value: 20,
      type: "美宜佳"
    },
    {
      key: "🍇",
      value: 30,
      type: "全家"
    },
    {
      key: "🍇",
      value: 10,
      type: "美宜佳"
    },
    {
      key: "🍐",
      value: 30,
      type: "全家"
    },
    {
      key: "🍐",
      value: 50,
      type: "美宜佳"
    }
  ];
  const chart = new Chart({
    container: "testChart",
    height: 300,
    padding: [20, 20, 50, 50],
    autoFit: true
  });
  chart.data(data);
  chart.scale({
    value: {
      min: 0,
      nice: true
    }
  });
  chart.legend({
    position: "bottom"
  });
  chart
    .line()
    .position("key*value")
    .color("type", ["#14deff", "red"])
    .tooltip("key*value", (a: any, b: any) => ({
    name: "自定义" + a,
    value: b
  }));
  chart
    .point()
    .position("key*value")
    .color("type", ["#14deff", "red"])
    .size(3)
    .style({
    fill: "#14deff"
  });
  chart.render();
}
```

## 生成随机数的数组

> `from()` 方法用于通过拥有 `length` 属性的对象或可迭代的对象来返回一个数组。
>
> `~~(Math.random() * 5)`生成 100 个 0~5 之间的随机数。`~~`是向下取整的简写语法
>
> 通过`Set`去重

```js
arr = [
  ...new Set(Array.from({ length: 100 }).map(() => ~~(Math.random() * 5))),
].map((item) => "d" + item);
```
