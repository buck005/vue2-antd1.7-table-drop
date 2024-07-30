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
        ],
        sourceCode: '',
        targetCode: '',
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
          mouseenter: event => {
            const ev = event || window.event;
            ev.target.draggable = true;
          },
          dragstart: event => {
            const ev = event || window.event;
            ev.stopPropagation();
            this.tableData.sourceCode = record.sourceCode;
            // console.log('sourceCode', this.tableData.sourceCode);
          },
          dragover: event => {
            const ev = event || window.event;
            ev.preventDefault();
          },
          dragenter: event => {
            const ev = event || window.event;
            ev.preventDefault();
            const list = document.getElementsByClassName('ant-table-row');
            const node = document.getElementsByClassName('target');
            if (node.length) {
              node[0].classList.remove('target');
            }
            list[index].classList.add('target');
          },
          drop: event => {
            const ev = event || window.event;
            ev.stopPropagation();
            this.tableData.targetCode = record.targetCode;
            console.log('targetCode', this.tableData.targetCode);
            const node = document.getElementsByClassName('target');
            if (node.length) {
              node[0].classList.remove('target');
            }
            const list = this.tableData.list.slice();
            const sourceIndex = list.findIndex(item => item.sourceCode === this.tableData.sourceCode);
            const sourceItem = list[sourceIndex];
            list.splice(sourceIndex, 1);
            const targetIndex = list.findIndex(item => item.targetCode === this.tableData.targetCode);

            if (sourceIndex === targetIndex) {
              list.splice(targetIndex + 1, 0, sourceItem);
            } else {
              list.splice(targetIndex, 0, sourceItem);
            }
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
