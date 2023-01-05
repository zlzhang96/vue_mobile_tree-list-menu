<template>
  <ul v-if="data.length">
    <li
      v-for="(item, i) in data"
      :key="i"
      @click.stop="selectItem(item)"
      v-show="expandFlag"
    >
      <div class="item">
        <!-- 展开的图标 -->
        <i
          class="expandIcon"
          @click.stop="expandItem(item, i)"
          :class="[
            expandArr.includes(i) ? 'reduce' : 'add',
            item.children && item.children.length ? '' : 'disabled',
          ]"
        >
        </i>
        <!-- 选项名 -->
        <h1>{{ item[labelKey] }}</h1>
        <!-- 选择的图标 -->
        <i
          class="selectIcon"
          :class="[
            value[valueKey] == item[valueKey] ? 'checked' : 'noChecked',
            item[disabledKey] ? 'disabled' : '',
          ]"
        >
        </i>
      </div>
      <list-menu
        v-if="item.children"
        @input="input"
        :data="item.children"
        :valueKey="valueKey"
        :labelKey="labelKey"
        :disabledKey="disabledKey"
        :value="value"
        :toastText="toastText"
        :expandFlag="expandArr.includes(i)"
      />
    </li>
  </ul>
</template>
<script>
export default {
  // 组件名必写
  name: "list-menu",
  props: {
    // 选中的值的属性名，必传
    valueKey: {
      type: String,
      required: true,
    },
    // 在页面要展示的选项属性名，必传
    labelKey: {
      type: String,
      required: true,
    },
    // 不可选的唯一标识，如item[disabledKey]为true则不可选择，非必传
    disabledKey: String,
    // 选中的值，必传
    value: {
      type: Object,
      required: true,
    },
    // 控制展开，不需要传
    expandFlag: {
      type: Boolean,
      default: true,
    },
    // 总数据，必传
    data: Array,
    // 不可选提示文字，非必传
    toastText: {
      type: String,
    },
  },
  data() {
    return {
      // 当前级组件已展开的项的索引
      expandArr: [],
    }
  },
  methods: {
    // 子组件逐级传递选中项
    input(item) {
      this.$emit("input", item)
    },
    // 选择
    selectItem(item) {
      // disable-key为1时表示不可选择该项目
      if (this.disabledKey && item[this.disabledKey]) {
        if (this.toastText) {
          alert(this.toastText)
        }
        return
      }
      this.$emit("input", item)
    },
    // 展开
    expandItem(item, i) {
      console.log("item", item)
      if (item.children && item.children.length) {
        let index = this.expandArr.indexOf(i)
        if (index > -1) {
          this.expandArr.splice(index, 1)
        } else {
          this.expandArr.push(i)
        }
      }
    },
  },
}
</script>
<style lang="less" scoped>
ul {
  width: 100%;
  position: relative;
  /* L_Mark: need to add this */
  box-sizing: border-box;
  color: pink;
  font-size: 14px;
  overflow: hidden;
  background: #fff;
  border-bottom: 0.8px solid #e1e1e1;
  list-style: none;
  padding-left: 0px;

  li {
    .item {
      box-sizing: border-box;
      // width: 100%;
      padding: 14/75rem 24/75rem;
      display: flex;
      align-items: center;
      .expandIcon {
        height: 1.2em;
        width: 1.2em;
        border: 1.5px solid;
        border-radius: 50%;
        position: relative;
        &:after {
          position: absolute;
          top: 50%;
          left: 50%;
          font-size: 12px;
          transform: translate(-50%, -50%);
        }
        &.add {
          border-color: #2a2a2a;
          &:after {
            color: #2a2a2a;
            content: "+";
          }
        }
        &.reduce {
          border-color: rgb(102, 175, 255);
          &:after {
            color: rgb(102, 175, 255);
            content: "-";
          }
        }
        &.disabled {
          border-color: #ddd;
          &:after {
            color: #ddd;
          }
        }
      }
      .selectIcon {
        height: 1.2em;
        width: 1.2em;
        border: 1.5px solid;
        border-radius: 50%;
        position: relative;
        &:after {
          position: absolute;
          top: 50%;
          left: 60%;
          transform: translate(-50%, -50%);
        }
        &.checked {
          border-color: rgb(102, 175, 255);
          background: rgb(102, 175, 255);
          position: relative;
          &:after {
            position: absolute;
            font-size: 0.8em;
            color: #fff;
            content: "✓";
            text-align: center;
            left: 0.6em;
          }
        }
        &.noChecked {
          border-color: rgb(102, 175, 255);
        }
        &.disabled {
          border-color: #ddd;
        }
      }
      h1 {
        margin-right: 6/75rem;
        padding: 0 0 0 1em;
        position: relative;
        top: 2/75rem;
        height: 60/75rem;
        line-height: 60/75rem;
        font-size: 30/75rem;
        flex: 1;
        white-space: nowrap;
        color: #2a2a2a;
        // .one-line;
        overflow-x: auto;
        transform: translateX(-0.5em);
      }
    }
    &:not(:first-child) {
      border-top: 0.8px solid #e1e1e1;
    }
    > ul {
      border-bottom: 0;
      padding-left: 2em;
      li {
        .item {
          padding-left: 12/75rem;
        }
        border-top: 0.8px solid #e1e1e1;
      }
    }
  }
}
</style>
