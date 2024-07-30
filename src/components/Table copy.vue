<template>
  <div>
    <a-table :columns="columns" :data-source="tableData.list" :custom-row="customRow"></a-table>
  </div>
</template>

<script>
const sharedOnCell = (_, index) => {
  if (index === 4) {
    return { colSpan: 0 };
  }
};
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data() {
    return {
      loading: false,
      selectedRowKeys: [],
      columns: [
        {
          title: 'Name',
          dataIndex: 'name',
          customCell: (_, index) => ({
            colSpan: index < 4 ? 1 : 5,
          }),
        },
        {
          title: 'Age',
          dataIndex: 'age',
          customCell: sharedOnCell,
        },
      ],
      tableData: {
        list: [
          {
            key: '1',
            name: 'John Brown',
            age: 32,
            tel: '0571-22098909',
            phone: 18889898989,
            address: 'New York No. 1 Lake Park',
          },
          {
            key: '2',
            name: 'Jim Green',
            tel: '0571-22098333',
            phone: 18889898888,
            age: 42,
            address: 'London No. 1 Lake Park',
          },
          {
            key: '3',
            name: 'Joe Black',
            age: 32,
            tel: '0575-22098909',
            phone: 18900010002,
            address: 'Sidney No. 1 Lake Park',
          },
        ], // 数据列表
        sourceCode: '', // 源目标code
        targetCode: '', // 目标code
      },
    };
  },
  methods: {
    onSelectChange(selectedRowKeys) {
      console.log('selectedRowKeys', selectedRowKeys);
    },
    customRow(record, index) {
      // console.log('customRow', record, index);
      return {
        props: {
          draggable: 'true',
        },
        style: {
          cursor: 'move',
        },
        on: {
          // 鼠标移入
          mouseenter: event => {
            const ev = event || window.event; // 兼容IE
            ev.target.draggable = true; // 让你要拖动的行可以拖动，默认不可以
          },
          // 开始拖拽
          dragstart: event => {
            const ev = event || window.event; // 兼容IE
            ev.stopPropagation(); // 阻止冒泡
            // 得到源目标数据序号
            this.tableData.sourceCode = record.sourceCode;
            // console.log('sourceCode', this.tableData.sourceCode);
          },
          // 拖动元素经过的元素
          dragover: event => {
            // 兼容 IE
            const ev = event || window.event;
            // 阻止默认行为
            ev.preventDefault();
          },
          // 拖动到达目标元素
          dragenter: event => {
            const ev = event || window.event;
            ev.preventDefault();
            // 设置拖动高亮效果
            const list = document.getElementsByClassName('ant-table-row');
            const node = document.getElementsByClassName('target');
            if (node.length) {
              node[0].classList.remove('target');
            }
            list[index].classList.add('target');
          },
          // 鼠标松开
          drop: event => {
            const ev = event || window.event; // 兼容IE
            ev.stopPropagation(); // 阻止冒泡
            // 得到目标数据序号
            this.tableData.targetCode = record.targetCode;
            console.log('targetCode', this.tableData.targetCode);
            // 取消高亮效果
            const node = document.getElementsByClassName('target');
            if (node.length) {
              node[0].classList.remove('target');
            }
            // 在备份数组上执行排序操作
            const list = this.tableData.list.slice();
            // 1. 通过code找到拖拽起始数据，备份它，并从数组中删除
            const sourceIndex = list.findIndex(item => item.sourceCode === this.tableData.sourceCode);
            const sourceItem = list[sourceIndex];
            list.splice(sourceIndex, 1);
            // 2. 通过code找到拖拽终点index，把备份的起始数据插入这个位置
            const targetIndex = list.findIndex(item => item.targetCode === this.tableData.targetCode);
            // const targetItem = list[targetIndex];

            if (sourceIndex === targetIndex) {
              list.splice(targetIndex + 1, 0, sourceItem);
            } else {
              list.splice(targetIndex, 0, sourceItem);
            }
            // 排序好的数组应用到响应式数据中
            this.tableData.list = list;
          },
        },
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
