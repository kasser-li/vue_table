<template>
  <div class="draggable" style="padding: 20px">
      <div class="n-dragTb" ref="tbBox">
    <el-table ref="sortableTable" row-key="id" :data="treeData"  style="width: 100%">
        <el-table-column  width="100" ></el-table-column>
      <el-table-column label="姓名" prop="name"></el-table-column>
      <el-table-column label="年龄" prop="age"></el-table-column>
        <el-table-column label="拖拽" width="100" >
            <template #default="{row}">
          <!-- <span :ref="el=>initRow(el,row)"><el-icon class="draggableIcon"><Rank /></el-icon></span> -->
          <span v-ref="({el})=>initRow(el,row)"><i class="el-icon-s-operation draggableIcon"></i></span>
          
            </template>
        </el-table-column>
        
    </el-table>
    <div class="n-dragTb-line" :style="{left:line.x+'px', top: line.y+'px'}" v-show="line.show"></div>
  </div>
  </div>
</template>
<script>
import Sortable from 'sortablejs';

export default {
  mounted() {
  window.vm = this;
      this.rowDrop()
   
  },
  data() {
      return {
    line:{
      type: null,
      center: null,
      show:false,
      x:9999,
      y:9999,
    },
          treeData: [
              {
                  id: 1,
                  level: 1,
                  name: '李一',
                  gender: '男',
                  age: 30,
                  job: "会计",
                  children: [{
                      id: 31,
                      level: 2,
                      name: '李一1',
                      gender: '2016-05-01',
                      age: '31',
                      job: '上海市普陀区金沙江路 1519 弄',
                  }, {
                      id: 32,
                      name: '李一2',
                      level: 2,
                      gender: '2016-05-01',
                      age: '32',
                      job: '上海市普陀区金沙江路 1519 弄'
                  }]
              },
              {
                  level: 1,
                  id: 2,
                  name: '王二',
                  gender: '未知',
                  age: 18,
                  job: "无业游民"
              },
              {
                  id: 3,
                  name: '张三',
                  gender: '男',
                  age: 50,
                  job: "老板",
                  level: 1,
                  children: [
          {
            id: 321,
            level: 2,
            name: '张三1',
            gender: '2016-05-01',
            age: '王小虎',
            job: '上海市普陀区金沙江路 1519 弄',
            children: [
              {
                id: 555,
                level: 3,
                name: '张三1  3级',
                gender: '2016-05-01',
                age: '21',
                job: '上海市普陀区金沙江路 1519 弄'
              }],
            
          }, {
            id: 322,
            level: 2,
            name: '张三2',
            gender: '2016-05-01',
            age: '22',
            job: '上海市普陀区金沙江路 1519 弄'
          },
        ]
              },
          ],
         
      }
  },
directives:{
  'ref':{
    bind(el, binding, vNode) {
      binding.value({el,binding, vm: vNode && vNode.componentInstance, vNode});
    },
    componentUpdated(el, binding, vNode, oldVNode){
      binding.value({el,binding, vm: vNode && vNode.componentInstance, vNode, oldVNode});
    },
    beforeMount(el, binding, vNode) {
      binding.value({el,binding, vm: vNode && vNode.componentInstance, vNode});
    },
    updated(el, binding, vNode, oldVNode){
      binding.value({el,binding, vm: vNode && vNode.componentInstance, vNode, oldVNode});
    }
  }
},
 
  methods: {
  // 初始化每个可拖动的行
  async initRow(el,row){
    await this.$nextTick()
    await this.$nextTick()
    const btn = el;
    while(el){
      if(el.classList.contains('el-table__row')) break;
      el = el.parentElement;
    }
    el && this.sortVm && this.sortVm.onInitRow(el,btn,row);
  },
  // 行拖拽
      rowDrop() {
    // 获取表格节点
    const el = this.$refs.sortableTable && this.$refs.sortableTable.$el.querySelector('.el-table__body-wrapper tbody')
    if (!el) return;
    
    // 为数据建立关联关系
    this.treeData.forEach(v=> v._parent = { id: 'root', level:0, children: this.treeData  });
    const arr = [...this.treeData];
    while(arr.length){
      const v = arr.shift();
      if(v.children) arr.push(...v.children.map(c=> (c._parent = v, c.level = v.level + 1,  c)));
    }
    
    const inden = 20;		//子级缩进
    
    
    
    
    // 调用sortable函数
    const sortVm = this.sortVm = Sortable.create(el, {
        animation: 0,
      sort:false,
      direction: 'vertical',
        handle: '.draggableIcon',
      draggable:'.el-table__row',
    })
    el.removeEventListener('pointerdown',sortVm._onTapStart);
    
    
    let dragRow;		//当前拖动的元素
    // 拖动结束后取消那个蓝色的校准线，清空当前拖动的元素
    el.ondragend = ()=> {
      const {line} = this;
      el.style.userSelect = '';
      if(!dragRow) return;
      dragRow.draggable = false;
      dragRow = false;
      if(line.center) line.center._el.style.background = '';
      line.center = line.type = line.show = false;
    }
    sortVm.onStart = el=> {
      // 开始拖动
      el.style.userSelect = 'none';
      (dragRow = el).draggable = true;
    }
    // 拖动过程中
    sortVm.onMove = (tg,e) =>{
      const {line,$refs:{tbBox}} = this;
      e.preventDefault();
      if(line.center) line.center._el.style.background = '';
      line.center = line.type = line.show = false;
      
      if(!dragRow || dragRow === tg || !dragRow._row || !tg._row ) return;
      const a = dragRow._row;
      const b = tg._row;
      
      const { clientY:my } = e;
      const {y,height:h,x} = tg.getBoundingClientRect();
      const {x:px,y:py} = tbBox.getBoundingClientRect();
      
      let type;
      if(my - y <= 3) type = 'top';
      else if(my >= y + h - 3 ) type = 'bottom'
      else type = 'center';
      
      if( type === 'center' &&  this.isChild(a,b)) return; 
      if( type === 'bottom' &&  a._parent === b) return; 
      
      line.type = type;
      
      if(type === 'center') return (line.center = b)._el.style.background = 'rgba(0,0,0,0.05)';
      
      line.show = true;
      line.x = x - px + inden * (b.level - 1);
      line.y = (type === 'top' ? y + 2 : y + h - 2) - py;  
      
    }
    // 拖动结束
    sortVm.onEnd = tg =>{
      const {line} = this;
      const {type} = line;
      if(line.center) line.center._el.style.background = '';
      line.center = line.type = line.show = false;
      if(!dragRow || dragRow === tg || !dragRow._row || !tg._row) return;
      const a = dragRow._row;
      const b = tg._row;
    
      if( type === 'center' &&  this.isChild(a,b)) return;
      if( type === 'bottom' &&  a._parent === b) return;
      
      const aIdx = a._parent.children.indexOf(a);
      a._parent.children.splice(aIdx,1);
      
      const bIdx = b._parent.children.indexOf(b);
      
      if(type === 'top') b._parent.children.splice(bIdx,0,a);
      if(type === 'center'){
        b.children = b.children || [];
        a._parent = b;
        a.level = b.level + 1;
        b.children.push(a)
      }
      if(type === 'bottom') b._parent.children.splice(bIdx+1,0,a);
      
      b._parent.children.forEach(v=> (v._parent = b._parent, v.level = b.level));
      
    }
    sortVm.onInitRow = (el,btn,row)=>{
      el._row = row;
      row._el = el;
      btn.onmousedown = e=>sortVm.onStart(el,e);
      el.ondrop = e=>sortVm.onEnd(el,e);
      el.ondragover = e=>sortVm.onMove(el,e);
       
    }
      },
  
  isChild(a,b){
    return (this.getTreePath({data: this.treeData, id: a.id}) || []).some(v=> v.id === b.id);
  },
  getTreePath({ data = [], id,  checkFunc, props,    format, paths = []  }){
    props = { children: 'children', id: 'id', ...(props || {}) };
    for(const c of data){
      if( checkFunc ? checkFunc({ id,item:c,data, props, paths }) : c[ props.id ] === id ) return (paths.push(format ?  format( {id,item:c,data, props, paths } ) : c), paths);
      if(!c[ props.children ]) continue;
      paths.push(format ?  format( {id,item:c,data, props, paths } ) : c);
      const res = this.getTreePath({ data: c[ props.children ]  ,id, checkFunc, props,  format,  paths });
      if(res) return res;
      paths.pop();
    }
    return false;
  },
  
  
  
  
  
  
  

  getUuiD(randomLength) {
      return Number(Math.random().toString().substr(2, randomLength) + Date.now()).toString(36)
  },
  
  }
}
</script>
<!-- <style scoped lang="scss">

.n-dragTb{
  position: relative;
  .n-dragTb-line{
    left: 0;
    right: 0;
    pointer-events: none;
    height: 1px;
    position: absolute;
    background: #4095ff;
  }
}
::v-deep.el-table__row--level-1 {
  background: #F5F5FC;
}
</style> -->