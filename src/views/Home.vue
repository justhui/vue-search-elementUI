<template>
  <div class="home">
    <h3>搜索组件：</h3>

    <search-form :options="options">
      <div slot="select" slot-scope="{ item, index }">
        <el-select
          v-model="value"
          placeholder="请选择"
          @change="change($event, item, index)"
        >
          <el-option label="1" value="1"> </el-option>
          <el-option label="2" value="2"> </el-option>
          <el-option label="3" value="3"> </el-option>
          <el-option label="4" value="4"> </el-option>
        </el-select>
      </div>
    </search-form>
  </div>
</template>

<script>
// @ is an alias to /src

const searchForm = () => import("./searchForm.vue");
export default {
  name: "Home",
  data() {
    return {
      value: "",
      options: [
        {
          label: "多选",
          key: "name1",
          children: [
            {
              label: "张三",
              value: "1",
            },
            {
              label: "李四",
              value: "2",
            },
            {
              label: "李四",
              value: "3",
            },
            {
              label: "李四",
              value: "4",
            },
            {
              label: "李四",
              value: "5",
            },
            {
              label: "李四",
              value: "6",
            },
            {
              label: "李四",
              value: "7",
            },
            {
              label: "李四",
              value: "8",
            },
            {
              label: "李四",
              value: "9",
            },
            {
              label: "李四",
              value: "10",
            },
            {
              label: "李四",
              value: "11",
            },
            {
              label: "李四",
              value: "12",
            },
            {
              label: "李四",
              value: "13",
            },
            {
              label: "李四",
              value: "14",
            },
            {
              label: "李四",
              value: "15",
            },
          ],
        },
        {
          label: "单选",
          key: "name2",
          type: "radio",
          children: [
            {
              label: "张三",
              value: "1",
            },
            {
              label: "李四",
              value: "2",
            },
          ],
        },
        {
          label: "输入框",
          key: "name3",
          slot: "input",
        },
        {
          label: "下拉",
          key: "name4",
          slot: "select",
        },
        {
          label: "插槽组合",
          key: "name5",
          children: [
            {
              label: "张三",
              value: "1",
            },
            {
              label: "李四",
              value: "2",
            },
          ],
          slot: "select",
        },
      ],
    };
  },

  components: {
    searchForm,
  },
  methods: {
    change(val, item, index) {
      this.options[index].checked = [val];
    },
  },
};
</script>
