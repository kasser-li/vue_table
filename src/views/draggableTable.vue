<template>
  <div>

   
  </div>
</template>
<script>
import Sortable from 'sortablejs'
export default {
  name: 'commonTable',
  data () {
    return {
      columns: [
        {
          title: 'Name',
          dataIndex: 'name',
          key: 'name',
          scopedSlots: { customRender: 'name' }
        },
        {
          title: 'Age',
          dataIndex: 'age',
          key: 'age',
          width: '12%',
          scopedSlots: { customRender: 'age' }
        },
        {
          title: 'Address',
          dataIndex: 'address',
          width: '30%',
          key: 'address',
          scopedSlots: { customRender: 'address' }
        }
      ],
      dataList: [
        {
          key: 1313123,
          id: 1,
          name: '1',
          age: 60,
          address: 'New York No. 1 Lake Park',
          children: [
            {
              key: 11,
              name: '2',
              age: 42,
              address: 'New York No. 2 Lake Park',
              id: '11'
            },
            {
              key: 12,
              name: '3',
              age: 30,
              address: 'New York No. 3 Lake Park',
              id: '12',
              children: [
                {
                  key: 121,
                  id: '121',
                  name: '4',
                  age: 16,
                  address: 'New York No. 3 Lake Park',
                }
              ],
            },
            {
              key: 13,
              id: '13',
              name: '5',
              age: 72,
              address: 'London No. 1 Lake Park',
              children: [
                {
                  key: 131,
                  id: '131',
                  name: '6',
                  age: 42,
                  address: 'London No. 2 Lake Park',
                  children: [
                    {
                      id: '1311',
                      key: 1311,
                      name: '7',
                      age: 25,
                      address: 'London No. 3 Lake Park',
                    },
                    {
                      id: '1312',
                      key: 1312,
                      name: '8',
                      age: 18,
                      address: 'London No. 4 Lake Park',
                    },
                  ],
                },
              ],
            },
          ],
        },
        {
          key: 2,
          id: '2',
          name: '9',
          age: 32,
          address: 'Sidney No. 1 Lake Park',
        }
      ],

    }
  },
  computed: {
  },
  methods: {
  },
  mounted () {
  }
}
</script>
<!-- <style lang="sass" scoped>
.func-bed{
  padding-bottom: 20px;
  .ant-btn-group{
    margin-left: 10px;
  }
}
</style> -->