<template>
  <div class="draggable" style="padding: 20px">
    <el-button @click="getTableData">打印表格数据</el-button>
    <el-button @click="addItem">新增一级节点</el-button>
    <el-table ref="sortableTable" row-key="id" border :data="tableData" :key="tableKey" style="width: 100%"
      :tree-props="{ children: 'children' }">
      <!-- <el-table-column width="50" align="center"></el-table-column> -->
      <el-table-column label="字段标签" prop="labelCurrent"></el-table-column>
      <el-table-column label="字段名" prop="labelName" align="center"></el-table-column>

      <el-table-column label="操作" width="100" align="center">
        <template slot-scope="scope">
          <span style="cursor:pointer;color:#100DB1 " @click="addItem(scope['row'], scope)">{{ '新增子节点' }}</span>
        </template>
      </el-table-column>
      <el-table-column label="拖拽" width="60" align="center">
        <template slot-scope="scope">
          <i class="el-icon-s-operation draggableIcon" title="拖拽"></i>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
import Sortable from 'sortablejs';

export default {
  mounted() {
    this.rowDrop()
  },
  data() {
    return {
      tableKey: 0,
      activeRows: [],
      tableData: [
        {
          id: 1,
          parentID: null,
          level: 1,
          labelCurrent: '1',
          labelName: 'labelName',
          gender: '男',
          age: 30,
          job: "会计",
          children: [
            {
              id: 11,
              parentID: 1,
              level: 2,
              labelCurrent: '11',
              labelName: 'labelName',
              gender: '2016-05-01',
              age: '王小虎',
              job: '上海市普陀区金沙江路 1519 弄',
            }, {
              id: 12,
              parentID: 1,
              labelCurrent: '12',
              labelName: 'labelName',
              level: 2,
              gender: '2016-05-01',
              age: '王小虎',
              job: '上海市普陀区金沙江路 1519 弄'
            }
          ]
        },
        {
          id: 2,
          parentID: null,
          level: 1,
          labelCurrent: '2',
          labelName: 'labelName',
          gender: '男',
          age: 30,
          job: "会计",
          children: [
            {
              id: 21,
              parentID: 2,
              level: 2,
              labelCurrent: '21',
              labelName: 'labelName',
              gender: '2016-05-01',
              age: '王小虎',
              job: '上海市普陀区金沙江路 1519 弄',
            }, {
              id: 22,
              parentID: 2,
              labelCurrent: '22',
              labelName: 'labelName',
              level: 2,
              gender: '2016-05-01',
              age: '王小虎',
              job: '上海市普陀区金沙江路 1519 弄'
            }
          ]
        },
        {
          id: 3,
          parentID: null,
          level: 1,
          labelCurrent: '3',
          labelName: 'labelName',
          gender: '男',
          age: 30,
          job: "会计",
          children: [
          ]
        },
        {
          id: 4,
          parentID: null,
          level: 1,
          labelCurrent: '4',
          labelName: 'labelName',
          gender: '男',
          age: 30,
          job: "会计",
          children: [
          ]
        },
      ],
      tableConfig: {
        tableItems: [
          {
            label: '字段标签',
            prop: 'labelCurrent',
          },
          {
            label: '字段名',
            prop: 'labelName',
          },
          {
            label: '性别',
            prop: 'gender',
          },
          {
            label: '年龄',
            prop: 'age',
          },
          {
            label: '工作',
            prop: 'job',
          },
        ]
      },
      baseIndex: 100,
    }
  },
  methods: {
    getTableData() {
      console.log(this.tableData);
    },
    getUuiD(randomLength) {
      return Number(Math.random().toString().substr(2, randomLength) + Date.now()).toString(36)
    },
    //   获取目标元素的父元素和目标元素的索引
    getItemParentAndIndex(arr, item) {

      let parent = null
      let index = -1

      for (let i = 0; i < arr.length; i++) {
        if (arr[i].id === item.id) {
          parent = arr[i]
          index = i
          break
        }
        if (Array.isArray(arr[i].children)) {
          let res = this.getItemParentAndIndex(arr[i].children, item)
          if (res[1] > -1) {
            parent = res[0]
            index = res[1]
          }
        }
      }
      // debugger
      return [parent, index]
    },

    // 添加二级
    addItem(r, scope) {
      console.log('scope', scope);
      let data = {
        id: this.getUuiD(),
        parentID: null,
        level: null,
        labelCurrent: '节点' + this.baseIndex,
        labelName: 'labelName',
        gender: '2016-05-01',
        age: '王小虎',
        job: '上海市普陀区金沙江路 1519 弄',
      }
      //   获取目标元素的父元素和目标元素的索引
      let [parent, index] = this.getItemParentAndIndex(this.tableData, r)
      if (index > -1) {
        data.level = parent.level + 1
        if (parent.hasOwnProperty('children')) {
          parent.children.push(data)
        } else {
          parent['children'] = [{ ...data }]
        }
      } else {
        data.level = 1
        this.tableData.push(data)
      }
      //   let idx = this.tableData.findIndex(e => e.id === r.id)
      //   if (idx > -1) {
      //       let item = this.tableData[idx]
      //       if (item.hasOwnProperty('children')) {
      //           item.children.push(data)
      //       } else {
      //           item['children'] = [{ ...data }]
      //       }
      //   }
      this.baseIndex++
      this.tableData = [...this.tableData]
    },
    // 行拖拽
    rowDrop() {
      // 获取表格节点
      // this.tableSortDestroy()
      this.$nextTick(() => {
        const el = this.$refs.sortableTable.$el.querySelector('.el-table__body-wrapper tbody')
        if (!el) { return }
        const _this = this
        // 插件调用函数
        Sortable.create(el, {
          animation: 300,
          handle: '.draggableIcon',
          onMove({ dragged, related }) {
            console.log({ dragged, related });
            _this.$set(_this, 'activeRows', _this.treeToTile(_this.tableData)) // 把树形的结构转为列表再进行拖拽
          },
          onEnd({ oldIndex, newIndex }) {
            console.log({ oldIndex, newIndex });
            
            const oldRow = _this.activeRows[oldIndex] // 移动的那个元素
            const newRow = _this.activeRows[newIndex] // 新的元素
            // console.log([ {id:oldRow.id,level:oldRow.level}, {id:newRow.id,level:newRow.level}]);
            console.log([ oldRow.level,newRow.level]);
            if (oldIndex !== newIndex && oldRow.id !== newRow.id) {
              // const modelProperty = _this.activeRows[oldIndex]
              const modelProperty = {...oldRow,level:newRow.level}

              const changeIndex = newIndex - oldIndex
              // const index = _this.activeRows.indexOf(modelProperty)
              const index = _this.activeRows.findIndex(e=> e.id == modelProperty.id)
              

              _this.activeRows.splice(index, 1)
              _this.activeRows.splice(index + changeIndex, 0, modelProperty)
              
              let lastNodeIndex = index + changeIndex 
              console.log('index + changeIndex',lastNodeIndex,_this.activeRows[lastNodeIndex]);
              if(modelProperty.level == 1){
                _this.activeRows[lastNodeIndex].parentID = null
              }else{
                // 在 activeRows 中找到 modelProperty最近的父级节点
                let parentLevel = modelProperty.level - 1
                let parentNode = _this.activeRows.findLast((e,eIndex)=>{return e.level == parentLevel && eIndex<lastNodeIndex})
                _this.activeRows[lastNodeIndex].parentID = parentNode.id
              }
              let ar = []
              _this.activeRows.map(e => {
                ar.push(e.id)
              })
              console.log(_this.activeRows);
              console.log(ar);
              
              _this.sortMenuData()
              if (oldRow.level === newRow.level && oldRow.level === 1) {
                // // 一级之间的拖拽
                // if (index < 0) {
                //     console.log('index < 0')
                //     return
                // }
                // _this.activeRows.splice(index, 1)
                // _this.activeRows.splice(index + changeIndex, 0, modelProperty)
                // _this.sortMenuData('1-1')
              } else if (oldRow.level === newRow.level && oldRow.level === 2) {
                // // 二级之间的拖拽
                // // _this.activeRows.splice(index, 1)
                // let res = [];
                // _this.tableData.forEach(t => {
                //     if (t.hasOwnProperty('children')) {
                //         // 在原来的一级下删除二级
                //         let oldChildIndex = t.children.findIndex(c => c.id === oldRow.id)
                //         if (oldChildIndex > -1) {
                //             t.children.splice(oldChildIndex, 1)
                //         }
                //         // 在新的一级下添加二级
                //         let newChildIndex = t.children.findIndex(c => c.id === newRow.id)
                //         if (newChildIndex > -1) {
                //             t.children = [...t.children.slice(0, newChildIndex), oldRow, ...t.children.slice(newChildIndex)]
                //         }
                //     }
                //     res.push(t)
                //     _this.tableData = res
                // })
                // _this.tableKey = Math.random()
                // _this.rowDrop()
              } else if (oldRow.level === 2 && newRow.level === 1) {
                // // 二级移动到一级
                // // console.log('2-1', oldRow, newRow)
                // let res = [];
                // _this.tableData.forEach(t => {
                //     if (t.hasOwnProperty('children')) {
                //         // 在原来的一级下删除二级
                //         let oldChildIndex = t.children.findIndex(c => c.id === oldRow.id)
                //         if (oldChildIndex > -1) {
                //             t.children.splice(oldChildIndex, 1)
                //         }
                //     }
                //     // 将提升为一级的二级放到相应位置
                //     let item = { ...oldRow, level: 1 }
                //     if (t.id === newRow.id) {
                //         res.push(t, item)
                //     } else {
                //         res.push(t)
                //     }
                //     _this.tableData = res
                // })
                // _this.tableKey = Math.random()
                // _this.rowDrop()
              } else if (oldRow.level === 1 && newRow.level === 2) {
                // // 一级移动到二级
                // console.log('1-2', oldRow, newRow)
                // let res = [];

                // let item = [{ ...oldRow, level: 2 }]
                // if (oldRow.hasOwnProperty('children')) {
                //     item = [...item, ...oldRow.children]
                //     delete item[0].children
                // }
                // _this.tableData.forEach(t => {
                //     // 将降为二级的目标以及他的children放到相应的位置
                //     if (t.hasOwnProperty('children')) {
                //         let newChildIndex = t.children.findIndex(c => c.id === newRow.id)
                //         if (newChildIndex > -1) {
                //             t.children = [...t.children.slice(0, newChildIndex), ...item, ...t.children.slice(newChildIndex)]
                //         }
                //     }
                //     if (t.id !== oldRow.id) {
                //         res.push(t)
                //     }
                // })
                // _this.tableData = res
                // _this.tableKey = Math.random()
                // _this.rowDrop()
              }

              // if (oldRow.level === newRow.level && oldRow.level === 1) {
              //     // 一级之间的拖拽
              //     if (index < 0) {
              //         console.log('index < 0')
              //         return
              //     }
              //     _this.activeRows.splice(index, 1)
              //     _this.activeRows.splice(index + changeIndex, 0, modelProperty)
              //     _this.sortMenuData('1-1')
              // } else if (oldRow.level === newRow.level && oldRow.level === 2) {
              //     // 二级之间的拖拽
              //     // _this.activeRows.splice(index, 1)
              //     let res = [];
              //     _this.tableData.forEach(t => {
              //         if (t.hasOwnProperty('children')) {
              //             // 在原来的一级下删除二级
              //             let oldChildIndex = t.children.findIndex(c => c.id === oldRow.id)
              //             if (oldChildIndex > -1) {
              //                 t.children.splice(oldChildIndex, 1)
              //             }
              //             // 在新的一级下添加二级
              //             let newChildIndex = t.children.findIndex(c => c.id === newRow.id)
              //             if (newChildIndex > -1) {
              //                 t.children = [...t.children.slice(0, newChildIndex), oldRow, ...t.children.slice(newChildIndex)]
              //             }
              //         }
              //         res.push(t)
              //         _this.tableData = res
              //     })
              //     _this.tableKey = Math.random()
              //     _this.rowDrop()
              // } else if (oldRow.level === 2 && newRow.level === 1) {
              //     // 二级移动到一级
              //     // console.log('2-1', oldRow, newRow)
              //     let res = [];
              //     _this.tableData.forEach(t => {
              //         if (t.hasOwnProperty('children')) {
              //             // 在原来的一级下删除二级
              //             let oldChildIndex = t.children.findIndex(c => c.id === oldRow.id)
              //             if (oldChildIndex > -1) {
              //                 t.children.splice(oldChildIndex, 1)
              //             }
              //         }
              //         // 将提升为一级的二级放到相应位置
              //         let item = { ...oldRow, level: 1 }
              //         if (t.id === newRow.id) {
              //             res.push(t, item)
              //         } else {
              //             res.push(t)
              //         }
              //         _this.tableData = res
              //     })
              //     _this.tableKey = Math.random()
              //     _this.rowDrop()
              // } else if (oldRow.level === 1 && newRow.level === 2) {
              //     // 一级移动到二级
              //     console.log('1-2', oldRow, newRow)
              //     let res = [];

              //     let item = [{ ...oldRow, level: 2 }]
              //     if (oldRow.hasOwnProperty('children')) {
              //         item = [...item, ...oldRow.children]
              //         delete item[0].children
              //     }
              //     _this.tableData.forEach(t => {
              //         // 将降为二级的目标以及他的children放到相应的位置
              //         if (t.hasOwnProperty('children')) {
              //             let newChildIndex = t.children.findIndex(c => c.id === newRow.id)
              //             if (newChildIndex > -1) {
              //                 t.children = [...t.children.slice(0, newChildIndex), ...item, ...t.children.slice(newChildIndex)]
              //             }
              //         }
              //         if (t.id !== oldRow.id) {
              //             res.push(t)
              //         }
              //     })
              //     _this.tableData = res
              //     _this.tableKey = Math.random()
              //     _this.rowDrop()
              // }
            }
          }
        })
      })
    },
    // sortMenuData(type) {
    //     if (type === '1-1') {
    //         let res = []
    //         this.activeRows.forEach(r => {
    //             if (r.level === 1) {
    //                 let itemIdx = this.tableData.findIndex(t => t.id === r.id)
    //                 if (itemIdx > -1) {
    //                     res.push({ ...this.tableData[itemIdx] })
    //                 }
    //             }
    //         })
    //         this.tableData = res
    //     }
    //     this.tableKey = Math.random()  //狠狠的刷新dom
    //     this.rowDrop() // 再把拖拽的功能塞入
    // },

    buildTree(arrs) {
      // 创建一个字典以便快速查找
      const map = new Map();
      const result = [];

      // 将每个元素添加到字典中，key 为 id
      arrs.forEach(item => {
        map.set(item.id, { ...item, children: [] });
      });

      // 遍历每个元素，将其添加到相应的 parent 的 children 中
      arrs.forEach(item => {
        const node = map.get(item.id);
        if (item.parentID !== null) {
          const parentNode = map.get(item.parentID);
          if (parentNode) {
            // console.log(parentNode, 'parentNode');
            node.parentID = parentNode.id
            parentNode.children.push(node);
          }
        } else {
          result.push(node);
        }
      });

      return result;
    },
    sortMenuData() {
      // let res = []
      // this.activeRows.forEach(r => {
      //   if (r.level === 1) {
      //     let itemIdx = this.tableData.findIndex(t => t.id === r.id)
      //     if (itemIdx > -1) {
      //       res.push({ ...this.tableData[itemIdx] })
      //     }
      //   }
      // })
      // this.tableData = res
      // console.log(this.activeRows);
      // let ar = []
      // this.activeRows.map(e => {
      //   ar.push(e.id)
      // })
      // console.log(ar);
      let arrs = this.buildTree(this.activeRows)
      console.log(arrs);
      this.tableData = arrs
      this.tableKey = Math.random()  //狠狠的刷新dom
      this.rowDrop() // 再把拖拽的功能塞入
    },

    treeToTile(treeData, childKey = 'children') { // 将树数据转化为平铺数据
      const arr = []
      const expanded = data => {
        if (data && data.length > 0) {
          data.filter(d => d).forEach(e => {
            const child = e[childKey]
            // e[childKey] = []
            arr.push(e)
            expanded(child || [])
          })
        }
      }
      expanded(treeData)
      // console.log('treeToTile', arr)
      return arr
    },
  }
}
</script>
<style scoped >
.draggableIcon {
  cursor: pointer;
}
</style>