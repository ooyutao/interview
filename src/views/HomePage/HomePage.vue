<template>
  <div class="content-panel" id="contentPanel">
    <div class="content-query">
      <input type="text" v-model="queryText" placeholder="请输入搜索内容">
      <button @click="getMainData">搜索</button>
    </div>
    <!-- tree container -->
    <div class="tree-container" id="treeContainer">
      <vue2-org-tree style="width:calc(100% - 30px);height: calc(100% - 30px);" :data="mainData" labelWidth="160"
        :horizontal="true" :label-class-name="labelClassName" collapsable @on-expand="onExpand"
        :render-content="renderContent" />
    </div>
    <!-- 缩略图容器 -->
    <!-- page nav panel -->
    <div class="page-nav-panel" id="pageCanvas" :class="!showViewPanel && 'close-panel'">
      <div class="title">
        <span @click.stop="onViewPanel">
          {{ showViewPanel ? '收起' : '展开' }}导航器
          <i class="el-icon-arrow-down" v-show="showViewPanel"></i>
          <i class="el-icon-arrow-up" v-show="!showViewPanel"></i>
        </span>
      </div>
      <!-- 全景图盒子 -->
      <!-- 缩略图 -->
      <transition name="viewPanel">
        <div class="view-img-box" id="viewCanvas" v-show="showViewPanel" :class="'bigHeightView'">
          <!-- 缩略图滑动方块 -->
          <div id="viewEye"></div>
        </div>
      </transition>
    </div>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref, set, computed } from 'vue'
import html2canvas from 'html2canvas'
import Vue2OrgTree from 'vue2-org-tree'
import 'vue2-org-tree/dist/style.css'
const data: any = ref({
  id: 0,
  label: "京东集团-京东物流",
  level:'0',
  expand: true,
  children: [
    {
      id: 1,
      label: "产品研发部",
      level:'1',
      expand: true,
      children: [
        {
          id: 5,
          label: "研发-前端",
          level:'2',
        },
        {
          id: 6,
          label: "研发-后端",
          level:'2',
        },
        {
          id: 9,
          label: "UI设计",
          level:'2',
        },
        {
          id: 3,
          label: "销售部",
          level:'2',
          expand: true,
          children: [
            {
              id: 7,
              label: "销售一部",
              level:'3',
              expand: true,
              children: [
                {
                  id: 7,
                  label: "销售一部",
                  level:'4',
                  expand: true,
                  children: [
                    {
                      id: 7,
                      label: "销售一部",
                      level:'5',
                      expand: true,
                      children: [
                        {
                          id: 7,
                          label: "销售一部",
                          level:'6',
                        },
                        {
                          id: 8,
                          label: "销售二部",
                          level:'6',
                        },
                      ]
                    },
                    {
                      id: 8,
                      label: "销售二部",
                      level:'5',
                    },
                  ]
                },
                {
                  id: 8,
                  label: "销售二部",
                  level:'4',
                },
              ]
            },
            {
              id: 8,
              label: "销售二部",
              level:'3',
            }
          ]
        },
      ]
    },
    {
      id: 3,
      label: "销售部",
      level:'1',
      expand: true,
      children: [
        {
          id: 7,
          label: "销售一部",
          level:'2',
          expand: true,
          children: [
            {
              id: 7,
              label: "销售一部",
              level:'3',
              expand: true,
              children: [
                {
                  id: 7,
                  label: "销售一部",
                  level:'4',
                  expand: true,
                  children: [
                    {
                      id: 7,
                      label: "销售一部",
                      level:'5',
                    },
                    {
                      id: 8,
                      label: "销售二部",
                      level:'5',
                    },
                  ]
                },
                {
                  id: 8,
                  label: "销售二部",
                  level:'4',
                },
              ]
            },
            {
              id: 8,
              label: "销售二部",
              level:'3',
            },
          ]
        },
        {
          id: 8,
          label: "销售二部",
          level:'2',
        }
      ]
    },
    {
      id: 4,
      label: "财务部",
      level:'1',
    },
    {
      id: 9,
      label: "HR人事",
      level:'1',
    },
    {
      id: 10,
      label: "财务部",
      level:'1',
    },
    {
      id: 11,
      label: "HR人事",
      level:'1',
    }
  ]
});
// 一个计算属性
const queryText:any = ref('');
const mainData:any = ref(null);

function getMainData() {
  if(queryText.value){
    const newTree = searchTree([data.value], queryText.value);
    if(newTree && newTree[0]){
      mainData.value = newTree[0];
    }else{
      mainData.value = JSON.parse(JSON.stringify(data.value));
    }
  }else{
    mainData.value = JSON.parse(JSON.stringify(data.value));
  }
  init();
}
// 递归查询
function searchTree(tree, searchTerm) {
  const newTree = [];
  for (const node of tree) {
    const newNode:any = JSON.parse(JSON.stringify(node));

    if (newNode.label.includes(searchTerm)) {
      newTree.push(newNode);
    }else
    if (newNode.children) {
      newNode.children = searchTree(newNode.children, searchTerm);
      if (newNode.children.length > 0) {
        newTree.push(newNode);
      }
    }
  }
  return newTree;
}

// 获取展开状态
function collapse(list) {
  list.forEach(function (child) {
    if (child.expand) {
      child.expand = false;
    }
    child.children && collapse(child.children);
  });
}
// 鼠标点击
function onExpand(e, data) {
  console.log(e)
  if ("expand" in data) {
    data.expand = !data.expand;
    if (!data.expand && data.children) {
      collapse(data.children);
    }
  } else {
    set(data, "expand", true);
  }
  init()
}

// 缩略图实现  this.showViewPanel = !this.showViewPanel
const showViewPanel: any = ref(true);
function onViewPanel(){
  showViewPanel.value = !showViewPanel.value;
  if(showViewPanel.value){
    init();
  }
}

onMounted(() => {
  getMainData();
})
function init() {
  setTimeout(() => {
    clearDraw();
    drawCanvasImg();
  }, 10);
}
// 清空画布
function clearDraw() {
  // 如果存在画布，先进行清空
  const canvasEl: any = document.getElementById('pageViewCanvas')
  if (canvasEl) {
    const canRect: any = canvasEl.getBoundingClientRect()
    const ctx: any = canvasEl.getContext('2d')
    ctx.clearRect(0, 0, canRect.width, canRect.height) // 清除以(0,0)为起始点，宽700，高600的矩形里的内容
    ctx.beginPath()
  }
}
// 绘制画布
function drawCanvasImg() {
  try {
    const viewEl: any = document.getElementById('viewCanvas')?.getBoundingClientRect()
    // 要预览的整体 dom
    const treeEl: any = document.getElementById('treeContainer')?.getBoundingClientRect()
    // 计算预览图的等比缩放比例
    const cWidthScale = viewEl.width / treeEl.width
    const cHeightScale = viewEl.height / treeEl.height
    // 创建画布
    const canvasEl: any = document.getElementById('pageViewCanvas')
      ? document.getElementById('pageViewCanvas')
      : document.createElement('canvas')

    canvasEl.width = treeEl.width * cWidthScale
    canvasEl.height = treeEl.height * cHeightScale
    canvasEl.id = 'pageViewCanvas'
    const canvasContext = canvasEl.getContext('2d')
    //  等比缩放绘图
    canvasContext.scale(cWidthScale, cHeightScale)
    const treeContainer: any = document.querySelector('#treeContainer');
    html2canvas(treeContainer, {
      canvas: canvasEl,
      scale: 1,
      useCORS: true // 是否使用图片跨域
    }).then(canvas => {
      document.getElementById('viewCanvas')?.appendChild(canvas)
      setTimeout(() => {
        // 绘制完成注册鼠标事件 
        initDomEvent()
      }, 50)
    })
  } catch (error) { }
}
// 绘制 viewEye
function setViewEye() {
  try {
    const eye: any = document.getElementById('viewEye')
    const contentEl: any = document.getElementById('contentPanel')
    const u = contentEl.getBoundingClientRect().width
    const d = contentEl.getBoundingClientRect().height

    const pageViewCEl: any = document.getElementById('pageViewCanvas')
    const g = pageViewCEl.getBoundingClientRect().width
    const n = pageViewCEl.getBoundingClientRect().height

    const treeCEl: any = document.getElementById('treeContainer')
    const f = treeCEl.getBoundingClientRect().width
    const m = treeCEl.getBoundingClientRect().height
    const l = 0
    let h = l - contentEl.scrollLeft
    let t = h + f
    if (h < 0) {
      h = 0
    } else {
      if (h > u) {
        h = u
      }
    }
    if (t > u) {
      t = u
    } else {
      if (t < 0) {
        t = 0
      }
    }
    let j = l - contentEl.scrollTop
    let e = j + m
    if (j < 0) {
      j = 0
    } else {
      if (j > d) {
        j = d
      }
    }
    if (e > d) {
      e = d
    } else {
      if (e < 0) {
        e = 0
      }
    }
    const i = t - h
    const p = e - j
    if (i === 0 || p === 0) {
      eye.style.display = 'none'
    } else {
      let k = contentEl.scrollLeft - l
      if (k < 0) {
        k = 0
      }
      k = k * (g / f)
      let q = contentEl.scrollTop - l
      if (q < 0) {
        q = 0
      }
      q = q * (n / m)
      const s = i * (g / f),
        c = p * (n / m)
      eye.style.left = k - 1 + 'px'
      eye.style.top = q - 1 + 'px'
      eye.style.width = s + 'px'
      eye.style.height = c + 'px'
      // eye.style.width = '290px'
      // eye.style.height = '240px'
      eye.style.display = 'show'
    }
  } catch (error) { }
}

// 初始化页面事件监听
function initDomEvent() {
  try {
    // tree 父容器
    const cEl: any = document.getElementById('contentPanel')
    // 预览图上的导航视窗
    const eyeEl: any = document.getElementById('viewEye')
    // tree 容器
    const treeCEl: any = document.getElementById('treeContainer')
    // 预览图 canvas
    const pageViewCEl: any = document.getElementById('pageViewCanvas')

    const setView = () => {
      setViewEye()
    }
    // 手动页面滚动时，重新绘制 视图框位置
    cEl.addEventListener('scroll', setView)
    // 视窗注册鼠标按下事件

    eyeEl.addEventListener('mousedown', m => {
      const left = eyeEl.offsetLeft
      const top = eyeEl.offsetTop
      const treeCElRect = treeCEl.getBoundingClientRect()
      const canvasRect = pageViewCEl.getBoundingClientRect()
      // 解绑 tree 父容器的 scroll 事件
      cEl.removeEventListener('scroll', setView)

      const k = cEl.scrollTop,
        d = cEl.scrollLeft,
        e = treeCElRect.width,
        a = treeCElRect.height,
        i = canvasRect.width,
        c = canvasRect.height,
        l = e / i,
        h = a / c

      const setVieLeftAndTop = q => {
        const o = q.pageX - m.pageX
        const s = q.pageY - m.pageY,
          r = d + o * l
        cEl.scrollLeft = r
        const p = k + s * h
        cEl.scrollTop = p
        eyeEl.style.left = left + o + 'px'
        eyeEl.style.top = top + s + 'px'
      }
      document.addEventListener('mousemove', setVieLeftAndTop)
      // 鼠标抬起卸载事件
      const rmViewEvent = () => {
        document.removeEventListener('mousemove', setVieLeftAndTop)
        document.removeEventListener('mouseup', rmViewEvent)
        setViewEye()
        // tree 父容器重新注册 scroll 事件
        cEl.addEventListener('scroll', setView)
      }
      document.addEventListener('mouseup', rmViewEvent)
    })

    setViewEye()
  } catch (error) {
    console.log(error)
  }
}


function renderContent(h, data) {
  // console.log(h());
  const children:any = data.children?.length ? `(${data.children.length})` : '';
  return `${data.label}${children}`
};
// 设置颜色
function labelClassName(data) {
  console.log(data)
  
  return 'bg-color-'+data.level
}
</script>
<style lang="scss" scoped>
:deep() {
  .org-tree-node-label-inner{
    font-size: 12px;
  }
  .bg-color-0{
    background-color: #0091ff;
    color: #fff;
  }
  .bg-color-1{
    border: 1px solid #0091ff;
  }
  .bg-color-2{
    border: 1px solid #ffbb00;
  }
  .bg-color-3{
    border: 1px solid #00eeff;
  }
  .bg-color-4{
    border: 1px solid #9900ff;
  }
  .bg-color-5{
    border: 1px solid #0033ff;
  }
  .bg-color-6{
    border: 1px solid #ff002b;
  }
}

.content-panel {
  background-color: #fff;
  height: calc(100% - 80px);
  width: 100%;
  overflow: auto;
  position: relative;
  padding-top: 60px;
  .content-query{
    background-color: #eee;
    border-radius: 3px;
    padding: 10px;
    position:absolute;
    left:10px;
    top:10px;
    z-index: 40;
    button{
      margin: 0 10px;
      cursor: pointer;
    }
  }

  #treeContainer {
    min-height: 100%;
    min-width: 100%;
  }

  .tree-container {
    display: flex;
    width: max-content;
    min-width: 100%;
  }

  .page-nav-panel {
    z-index: 30;
    padding: 10px 0px;
    position: fixed;
    right: 30px;
    bottom: 30px;
    border: 1px solid #999;
    border-radius: 4px;
    background-color: #eee;

    &.close-panel {
      right: 30px;
      bottom: 30px;

      .title {
        margin-bottom: 0;
      }
    }

    .title {
      font-size: 12px;
      color: #333;
      text-align: right;
      margin-bottom: 6px;
      padding-right: 10px;
      padding-left: 10px;

      span {
        cursor: pointer;
      }
    }

    .view-img-box {
      width: 480px;
      height: 130px;
      margin: 0 30px;
      position: relative;

      &.bigHeightView {
        width: 290px;
        height: 240px;
        margin: 0 10px;
      }

      #viewEye {
        position: absolute;
        background: rgba(1, 1, 1, .1);
        opacity: 0.4;
        border: 1px solid #0091ff;
        border-radius: 4px;
        cursor: move;
      }
    }
  }
}

</style>
