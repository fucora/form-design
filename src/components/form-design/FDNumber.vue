<template>
  <el-form label-position="top" label-width="80px">
    <el-form-item label="标题">
      <el-input v-model="data.title" size="small"></el-input>
    </el-form-item>
    <el-form-item label="宽度">
      <el-input v-model="data.options.width" size="small"></el-input>
    </el-form-item>
    <el-form-item label="最小值">
      <el-input-number v-model="data.options.min" :min="0" :max="100" label="描述文字" size="small"></el-input-number>
    </el-form-item>
    <el-form-item label="最大值">
      <el-input-number v-model="data.options.max" :min="0" :max="100" label="描述文字" size="small"></el-input-number>
    </el-form-item>
    <el-form-item label="数据绑定key">
      <el-input v-model="data.key" size="small" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="是否必填">
      <el-switch v-model="data.options.required" active-color="#13ce66" inactive-color="#EEEEEE"></el-switch>
    </el-form-item>
    <el-form-item label="是否禁用">
      <el-switch v-model="data.options.disabled" active-color="#13ce66" inactive-color="#EEEEEE"></el-switch>
    </el-form-item>
  </el-form>
</template>

<script>
import common from '@/utils/common';
import bus from '@/utils/bus';

export default {
  props: {
    propData: {
      type: Object,
      default: function () {
        return {
          title: '计数器',
          type: 'number',
          icon: '/src/assets/img/form-design/number.png',
          options: {
            width: "120px",
            min: 0,
            max: 100,
            required: false,
            disabled: false,
          },
          key: common.getGuid()
        }
      }
    }
  },
  data() {
    return {
      data: {
        title: '计数器',
        type: 'number',
        icon: '/src/assets/img/form-design/number.png',
        options: {
          width: "120px",
          min: 0,
          max: 100,
          required: false,
          disabled: false,
        },
        key: common.getGuid()
      }
    }
  },
  methods: {

  },
  watch: {
    data: {
      handler: function (value, oldValue) {
        let newFormList = common.deepClone(
          this.$store.state.formDesign.formList
        );
        let activeIndex;
        for (let i = 0; i < this.$store.state.formDesign.formList.length; i++) {
          const element = newFormList[i];
          if (element.type !== 'grid') {
            if (element.key === this.$store.state.formDesign.activeKey) {
              activeIndex = i;

              newFormList[i] = value;
              this.$store.commit("formDesign/updateActiveKey", element.key);
              this.$store.dispatch("formDesign/setFormList", common.deepClone(newFormList));
              bus.$emit("formDesign.syncList", common.deepClone(newFormList));
              break;
            }
          } else {
            for (let j = 0; j < element.cols.length; j++) {
              const element2 = element.cols[j];
              for (let k = 0; k < element2.list.length; k++) {
                const element3 = element2.list[k];
                if (element3.key === this.$store.state.formDesign.activeKey) {
                  activeIndex = i;

                  newFormList[i].cols[j].list[k] = value;
                  this.$store.commit("formDesign/updateActiveKey", element3.key);
                  this.$store.dispatch("formDesign/setFormList", common.deepClone(newFormList));
                  bus.$emit("formDesign.syncList", common.deepClone(newFormList));
                  break;
                }
              }
            }
          }

        }
      },
      deep: true
    },
    propData: {
      handler: function (value) {
        this.data = common.deepClone(value);
      },
      deep: true,
      // immediate: true
    }
  }
}
</script>

<style>
</style>
