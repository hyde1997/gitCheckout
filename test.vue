<!-- 策略配置页面 -->
<template>
  <!-- 第三页的表单 -->
  <div>
    <div
      v-for="(group, index) in strategyData.groups"
      :key="index"
      class="settingsBox"
    >
      <h4 class="itemTitle">门户分流链接配置-{{ index + 1 }}</h4>
      <div class="infoBox">
        <mtd-form ref="formCustom" :model="group">
          <!-- 分组信息 -->
          <mtd-form-item
            :label="'分组' + `${index + 1}`"
            :label-width="200"
            class="verticalSpace leftSpace"
          >
            <span>分流规则：除余分流，</span>
            <span>除数：{{ strategyData.divisor }}，</span>
            <span>选择范围：{{ group.minValue }}~{{ group.maxValue }}</span>
          </mtd-form-item>
          <!-- flow选择 -->
          <mtd-form-item
            prop="pageTemplateId"
            label="申卡流程模板（flow）"
            :label-width="210"
            class="verticalSpace"
            :rules="{
              required: true,
              message: 'Flow配置必选!',
            }"
          >
            <EnhanceSelect
              class="rule-ruleName"
              type="text"
              data="strategyData[groups][arrIdx][pageTemplateId]"
              :value="group.pageTemplateId"
              call-back-func="setStrategyData"
              :index="[index]"
              clearable
              style="width: 300px"
              @change="handleFlowChange(index)"
            >
              <mtd-option
                v-for="option in FLOW_OPTIONS"
                :key="option.pageTemplateId"
                :label="option.pageTemplateName"
                :value="option.pageTemplateId"
              />
            </EnhanceSelect>
          </mtd-form-item>
          <!-- planID输入 -->
          <mtd-form-item
            label="申卡营销素材（planID）"
            :label-width="200"
            class="verticalSpace leftSpace"
          >
            <EnhanceInput
              type="text"
              data="strategyData[groups][arrIdx][planId]"
              :value="group.planId"
              :index="[index]"
              call-back-func="setStrategyData"
              clearable
              style="width: 300px"
            />
          </mtd-form-item>
        </mtd-form>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import { mapState, mapMutations } from 'vuex'
import _ from 'lodash'

import { EnhanceSelect, EnhanceInput } from '../utils/enhanceItem'
import Api from '../../../../services/portal'

export default {
  components: {
    EnhanceSelect,
    EnhanceInput,
  },
  data() {
    return {
      FLOW_OPTIONS: [],
    }
  },
  computed: {
    ...mapState({
      strategyData: (state) => state.portal.strategyData,
    }),
  },
  methods: {
    // 更新stroe的数据
    ...mapMutations(['setStrategyData']),
    init() {
      this.fetchFlowOptions()
    },
    async fetchFlowOptions() {
      try {
        const { code, data } = await Api.getFlowOptions()
        if (code === '0' && data) {
          this.FLOW_OPTIONS = data
          console.log(this.FLOW_OPTIONS, data)
        }
      } catch (error) {
        console.error(error)
      }
    },
    handleFlowChange(index) {
      const strategyData = _.cloneDeepWith(this.strategyData)
      strategyData.groups[index].planId = ''
      this.setStrategyData(strategyData)
    },
  },
  created() {
    this.init()
  },
}
</script>

<style lang="less" rel="stylesheet/less">
.mtd-form-item-label {
  text-align: left;
}
.settingsBox {
  margin: 0 60px;
}
.itemTitle {
  margin: 0;
}
.infoBox {
  margin-left: 60px;
}
.topSpace {
  margin-top: 40px;
}
.verticalSpace {
  margin: 20px 0;
}
.leftSpace {
  margin-left: 11px;
}
</style>
