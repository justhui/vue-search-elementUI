<template>
  <div class="search-form">
    <div
      class="search-item"
      v-for="(item, index) in dataOptions.slice(0, sliceDataNum)"
      :key="item.label"
    >
      <div class="title">
        <span>{{ item.label }}</span>
      </div>
      <div
        class="item-options"
        :class="{ 'is-down': item.isDown }"
        :ref="'options' + index"
      >
        <template v-if="item.children && item.children.length > 0">
          <!-- 单选 -->
          <template v-if="item.type">
            <el-radio-group
              v-model="item.checked"
              @change="selectItem($event, index, item)"
            >
              <el-radio label="">
                <el-tag>不限</el-tag>
              </el-radio>
              <el-radio
                v-for="val in item.children"
                :key="val.value || val"
                :label="val.value || val"
              >
                <el-tag>{{ val.label || val }}</el-tag>
              </el-radio>
            </el-radio-group>
          </template>

          <!-- 多选 -->
          <template v-else>
            <el-checkbox-group
              v-model="item.checked"
              @change="selectItem($event, index, item)"
            >
              <el-checkbox label="">
                <el-tag>不限</el-tag>
              </el-checkbox>

              <el-checkbox
                v-for="val in item.children"
                :key="val.value || val"
                :label="val.value || val"
              >
                <el-tag
                  :closable="
                    item.checked && item.checked.includes(val.value || val)
                  "
                  >{{ val.label || val }}</el-tag
                >
              </el-checkbox>
            </el-checkbox-group>
          </template>
        </template>

        <!-- 插槽部分 -->
        <template v-if="item.slot === 'input'">
          <el-input
            v-model="item.checked"
            @blur="selectItem(item.checked, index, item)"
            @keyup.native.enter="selectItem(item.checked, index, item)"
            placeholder="请输入"
          ></el-input>
        </template>
        <template v-else>
          <slot :name="item.slot" :item="item" :index="index"></slot>
        </template>
      </div>
      <div class="down" v-show="item.isShow" @click="dropDown(index)">
        <span v-show="item.isDown" class="el-icon-arrow-up"></span>
        <span v-show="!item.isDown" class="el-icon-arrow-down"></span>
      </div>
    </div>
    <!-- 使用数据源判断 -->
    <div class="show-more" @click="showMore" v-if="options.length > 4">
      <span v-show="!isShowMore">
        <span class="el-icon-d-arrow-right"></span>
        <span>展开更多</span>
      </span>
      <span v-show="isShowMore">
        <span class="el-icon-d-arrow-left"></span>
        <span>收起更多</span>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "SearchForm",
  props: {
    options: {
      type: Array,
      default: () => [],
    },
    sliceNum: {
      type: Number,
      default: 4,
    },
  },
  data() {
    return {
      lineHeight: 40, //26*1.5 1.5倍左右的子元素行高
      sliceDataNum: this.sliceNum,
      isDown: false,
      isShowMore: false,
      checkboxGroup: [],
      dataOptions: [],
      sourceOptions: [],
      reusltObj: {},
    };
  },
  created() {
    this.dataOptions = JSON.parse(JSON.stringify(this.initData()));
  },
  mounted() {
    this.$nextTick(() => {
      // 通过获取 ref 指定的子元素 el-checbox-group的高度来判断展示图标
      this.dataOptions = this.initDomData();
    });
    // // 监听屏幕宽度
    window.onresize = () => {
      this.dataOptions = this.dataOptions.map((item, index) => {
        const ref = this.$refs["options" + index];
        const flag = ref && ref[0] && ref[0].children && ref[0].children[0];
        if (flag && ref[0].children[0].offsetHeight > this.lineHeight) {
          this.$set(item, "isShow", true);
        } else {
          this.$set(item, "isShow", false);
        }
        return item;
      });
    };
  },
  methods: {
    dropDown(index) {
      let isDown = this.dataOptions[index].isDown;
      this.$set(this.dataOptions[index], "isDown", !isDown);
    },
    // 初始化data
    initData() {
      let arr = [];
      const list = ["input"];
      arr = this.options.map((item) => {
        // 判断是否有默认选中
        if (!item.checked) {
          // slot 表示插槽
          if (item.slot && list.includes(item.slot)) {
            this.$set(item, "checked", "");
          } else {
            // type 表示单选
            let checked = item.type ? "" : [""];
            this.$set(item, "checked", checked);
          }
        }
        return item;
      });
      return arr;
    },
    // 初始化dom
    initDomData() {
      const arr = this.options.map((item, index) => {
        const ref = this.$refs["options" + index];
        const flag = ref && ref[0] && ref[0].children && ref[0].children[0];
        if (flag && ref[0].children[0].offsetHeight > this.lineHeight) {
          this.$set(item, "isShow", true);
          this.$set(item, "isDown", false);
        } else {
          this.$set(item, "isShow", false);
        }
        return item;
      });
      return arr;
    },
    // 展开与收起
    showMore() {
      if (!this.isShowMore) {
        this.sliceDataNum = this.options.length;
      } else {
        this.sliceDataNum = 4;
      }
      this.isShowMore = !this.isShowMore;
    },
    // 选择项
    selectItem(val, index = 0, item = {}) {
      let checked;
      // debugger;
      if (!item.type && Array.isArray(val)) {
        // 多选的时候需要过滤 ['']的《不限》选项
        if (val[val.length - 1] === "") {
          this.$set(this.dataOptions[index], "checked", [""]);
          checked = [];
        } else {
          checked = this.dataOptions[index].checked.filter((item) => item);
          // 处理[]的情况变成['']选中《不限》
          let defaultChecked = checked.length === 0 ? [""] : checked;
          this.$set(this.dataOptions[index], "checked", defaultChecked);
        }
      } else {
        checked = val;
      }

      // const key = this.dataOptions[index].key;
      // checked = this.dataOptions[index].checked.filter((item) => item);
      this.reusltObj[item.key] = checked;
      this.$emit("onSearch", this.reusltObj);
    },
  },
};
</script>

<style scoped lang="scss">
.search-form {
  background: #fff;
  padding: 20px;
  .search-item {
    display: flex;
    margin-bottom: 20px;
    .title {
      height: 32px;
      line-height: 32px;
      font-size: 15px;
      min-width: 80px;
    }
    .item-options {
      flex: 1;
      display: flex;
      flex-wrap: wrap;
      height: 32px;
      overflow: hidden;
      ::v-deep .el-checkbox-group {
        .el-checkbox {
          height: 32px;
          line-height: 32px;
          margin: 0;
          margin-right: 25px;
          margin-bottom: 5px;
        }

        .el-checkbox__input {
          display: none;
        }
        .el-tag {
          background-color: #fff;
          border: none;
          color: #333;
          display: inline-block;
          font-size: 12px;
          box-sizing: border-box;
          white-space: nowrap;
        }
      }
      ::v-deep .el-radio-group {
        .el-radio {
          height: 32px;
          line-height: 32px;
          margin: 0;
          margin-right: 25px;
          margin-bottom: 5px;
        }

        .el-radio__input {
          display: none;
        }
        .el-tag {
          background-color: #fff;
          border: none;
          color: #333;
          display: inline-block;
          font-size: 12px;
          box-sizing: border-box;
          white-space: nowrap;
        }
      }
      ::v-deep .el-input {
        margin-left: 10px;
        .el-input__inner {
          width: 210px;
          height: 32px;
          line-height: 32px;
        }
        .el-input__suffix {
          right: 15px;
          top: 3px;
        }
      }
      .is-checked {
        .el-tag {
          background-color: #e5f7f5;
          color: #04ae9a;
          border: 0;
        }
      }
    }
    .is-down {
      height: auto !important;
    }
    .down {
      border: 1px solid #dfe6ee;
      width: 24px;
      height: 24px;
      line-height: 24px;
      text-align: center;
      border-radius: 5px;
      cursor: pointer;
      margin-top: 2px;
    }
  }
  .show-more {
    width: 100%;
    text-align: center;
    padding: 10px 0;
    text-align: center;
    line-height: 20px;
    color: #bbc9d9;
    padding: 10px 0;
    background: #fff;
    font-size: 13px;
    cursor: pointer;
    ::v-deep .el-icon-d-arrow-right,
    ::v-deep .el-icon-d-arrow-left {
      transform: rotate(90deg);
      font-size: 15px;
      margin-right: 6px;
    }
  }
}
</style>