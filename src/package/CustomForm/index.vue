<template>
  <div class="custom-form">
    <el-form
      ref="ruleCustomForm"
      :label-position="labelPosition"
      :model="customForm"
      :label-width="labelWidth"
      class="demo-ruleForm"
      :size="size"
    >
      <!-- Layout 布局 -->
      <el-row :gutter="gutter" type="flex" style="flex-wrap: wrap">
        <template>
          <el-col
            v-for="item in formConfigSon"
            :key="item.id"
            :span="item.span || 24"
          >
            <!-- 自定义item slot -->
            <template v-if="['Slot', 'slot'].includes(item.type)">
              <div>
                <slot :name="item.name" :item="item"
                  >我是{{ item.name }}内容区域，请填写自定义item</slot
                >
              </div>
            </template>

            <template v-else>
              <div class="form-item">
                <el-form-item
                  :label="item.name"
                  :prop="item.prop"
                  :rules="item.rules || customFormrules[item.prop]"
                >
                  <template #label>
                    <span>
                      <span v-if="!item.labelConfig">{{ item.name }}</span>
                      <span v-else>
                        <span>
                          {{ item.labelConfig["name"] || item.name }}
                        </span>
                        <el-tooltip
                          class="item"
                          effect="dark"
                          :content="item.labelConfig['content']"
                          placement="top-start"
                        >
                          <el-button
                            type="text"
                            :style="
                              (item.labelConfig['handlestyle'] &&
                                item.labelConfig['handlestyle'](extraData)) ||
                              item.labelConfig['style']
                            "
                            :class="item.labelConfig['icon']"
                          />
                        </el-tooltip>
                      </span>
                    </span>
                  </template>
                  <!-- 输入框 -->
                  <el-input
                    v-if="['input', 'Input'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :class="item.isBottomLine ? 'input-style' : ''"
                    :style="{ width: item.width || '100%' }"
                    clearable
                    :maxlength="item.maxlength"
                    :show-word-limit="item.showWordLimit"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :size="size"
                    :placeholder="item.placeholder || `请输入${item.name}`"
                    :show-password="item.showPassword"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                    @input="
                      handleInput(
                        $event,
                        item.oninput,
                        item.input &&
                          item.input(customForm[item.prop], extraData),
                        item.prop
                      )
                    "
                  >
                    <template v-if="item.prependConfig" slot="prepend">
                      <span v-if="item.prependConfig['label']">
                        {{ item.prependConfig["label"] }}
                      </span>
                      <el-button
                        v-if="item.prependConfig['icon']"
                        :icon="item.prependConfig['icon']"
                      />
                    </template>
                    <template v-if="item.appendConfig" slot="append">
                      <span v-if="item.appendConfig['label']">
                        {{ item.appendConfig["label"] }}
                      </span>
                      <el-button
                        v-if="item.appendConfig['icon']"
                        :icon="item.appendConfig['icon']"
                      />
                    </template>
                  </el-input>
                  <!-- 计数器 -->
                  <el-input-number
                    v-else-if="
                      ['inputNumber', 'InputNumber'].includes(item.type)
                    "
                    v-model="customForm[item.prop]"
                    :size="size"
                    :step="item.step"
                    :step-strictly="item.stepStrictly"
                    :precision="item.precision"
                    :max="item.max"
                    :min="item.min"
                    :controls-position="item.controlsPosition"
                    :controls="item.controls"
                    :style="{ width: item.width || '100%' }"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  />
                  <!-- 下拉 -->
                  <el-select
                    v-else-if="['select', 'Select'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    v-el-select-lazyloading="item.lazyloading"
                    clearable
                    :style="{ width: item.width || '100%' }"
                    :size="size"
                    :multiple="item.multiple"
                    :filterable="item.filterable"
                    :reserve-keyword="item.reserveKeyword"
                    :loading="item.loading"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :remote="item.remote"
                    :remote-method="item.remoteMethod"
                    :placeholder="item.placeholder || `请输入${item.name}`"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  >
                    <el-option
                      v-for="op in item.options"
                      :key="op.value"
                      :label="op.label"
                      :value="op.value"
                    />
                  </el-select>
                  <!-- 文本域 多行输入 -->
                  <el-input
                    v-else-if="['textarea', 'Textarea'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    clearable
                    :style="{ width: item.width || '100%' }"
                    :size="size"
                    type="textarea"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :autosize="item.autosize"
                    :rows="item.rows"
                    :maxlength="item.maxlength"
                    :placeholder="item.placeholder || `请输入${item.name}`"
                    :show-word-limit="item.showWordLimit"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                    @input="
                      item.input && item.input(customForm[item.prop], extraData)
                    "
                  />
                  <!-- 图片 -->
                  <upload-images
                    v-else-if="['image', 'Image'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :limit="item.limit"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :handle-upload="handleUpload"
                  />
                  <!-- 日期时间 -->
                  <el-date-picker
                    v-else-if="
                      ['datetimePicker', 'DatetimePicker'].includes(item.type)
                    "
                    v-model="customForm[item.prop]"
                    :type="item.isRange ? 'datetimerange' : 'datetime'"
                    :prefix-icon="
                      item.prefixIcon ? item.prefixIcon : 'el-icon-date'
                    "
                    :start-placeholder="
                      item.startPlaceholder || `开始${item.name}`
                    "
                    :end-placeholder="item.endPlaceholder || `截止${item.name}`"
                    :placeholder="item.placeholder || `选择${item.name}`"
                    :size="size"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :range-separator="
                      item.rangeSeparator ? item.rangeSeparator : '~'
                    "
                    :value-format="item.valueFormat || 'yyyy-MM-dd HH:mm:ss'"
                    :style="{ width: item.width || '100%' }"
                    :picker-options="item.pickerOptions"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  />
                  <!-- 日期 -->
                  <el-date-picker
                    v-else-if="['datePicker', 'DatePicker'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :type="item.isRange ? 'daterange' : 'date'"
                    :prefix-icon="
                      item.prefixIcon ? item.prefixIcon : 'el-icon-date'
                    "
                    :start-placeholder="
                      item.startPlaceholder || `开始${item.name}`
                    "
                    :end-placeholder="item.endPlaceholder || `截止${item.name}`"
                    :placeholder="item.placeholder || `选择${item.name}`"
                    :size="size"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :range-separator="
                      item.rangeSeparator ? item.rangeSeparator : '~'
                    "
                    :value-format="item.valueFormat || 'yyyy-MM-dd HH:mm:ss'"
                    :style="{ width: item.width || '100%' }"
                    :picker-options="item.pickerOptions"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  />
                  <el-date-picker
                    v-else-if="['date', 'Date'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    type="date"
                    :value-format="item.valueFormat || 'yyyy-MM-dd HH:mm:ss'"
                    :style="{ width: item.width || '100%' }"
                    :picker-options="item.pickerOptions"
                    :placeholder="item.placeholder || `选择${item.name}`"
                    :size="size"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  />
                  <!-- 单选按钮 -->
                  <el-radio-group
                    v-else-if="['radioGroup', 'RadioGroup'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :size="item.size"
                    :style="{ width: item.width || '100%' }"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    @input="
                      item.input && item.input(customForm[item.prop], extraData)
                    "
                  >
                    <template>
                      <el-row>
                        <el-col
                          v-for="ra in item.radios"
                          :key="ra.value"
                          :span="item.vertical ? 24 : ra.span ? ra.span : 4"
                        >
                          <el-radio
                            :disabled="ra.disabled || item.disabled"
                            style="margin-right: 12px; padding: 3px 0"
                            :label="ra.value"
                            >{{ ra.label }}
                          </el-radio>
                        </el-col>
                      </el-row>
                    </template>

                    <!-- </div> -->
                  </el-radio-group>
                  <!-- 单选按钮样式 -->
                  <el-radio-group
                    v-else-if="
                      ['radioGroupButton', 'RadioGroupButton'].includes(
                        item.type
                      )
                    "
                    v-model="customForm[item.prop]"
                    :size="item.size"
                    :style="{ width: item.width || '100%' }"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    @input="
                      item.input && item.input(customForm[item.prop], extraData)
                    "
                  >
                    <div class="radio-group-style">
                      <el-radio-button
                        v-for="ra in item.radios"
                        :key="ra.value"
                        :label="ra.value"
                        :disabled="ra.disabled || item.disabled"
                        >{{ ra.label }}</el-radio-button
                      >
                    </div>
                  </el-radio-group>
                  <!-- 开关 -->
                  <el-switch
                    v-else-if="['switch', 'Switch'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :size="item.size"
                    :active-value="item.activeValue"
                    :inactive-value="item.inactiveValue"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  />
                  <!-- selectTree -->
                  <select-tree
                    v-else-if="['selectTree', 'SelectTree'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :placeholder="item.placeholder || `请选择${item.name}`"
                    :tree-props="item.treeProps"
                    :data="item.data"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :style="{ width: item.width || '100%' }"
                    :node-key="item.nodeKey"
                    :custom-id="item.customId"
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                  />
                  <!-- checkBox 单选 -->
                  <el-checkbox
                    v-else-if="['CheckBox', 'checkBox'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :size="item.size"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    @change="
                      item.change &&
                        item.change(customForm[item.prop], extraData)
                    "
                    >{{ item.label }}
                  </el-checkbox>
                  <!-- 地域选择器 -->
                  <el-cascader
                    v-else-if="['cascader', 'Cascader'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :size="item.size"
                    :style="{ width: item.width || '100%' }"
                    :options="regionOptions"
                    :placeholder="item.placeholder || `请输入${item.name}`"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    clearable
                  />
                  <radio-group-input
                    v-else-if="
                      ['radioGroupInput', 'RadioGroupInput'].includes(item.type)
                    "
                    v-model="customForm[item.prop]"
                    :vertical="item.vertical"
                    :radios="item.radios"
                    :size="item.size"
                    :width="item.width"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                  />
                  <checkbox-group-input
                    v-else-if="
                      ['checkboxGroupInput', 'CheckboxGroupInput'].includes(
                        item.type
                      )
                    "
                    v-model="customForm[item.prop]"
                    :vertical="item.vertical"
                    :options="item.options"
                    :size="item.size"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                    :width="item.width"
                  />
                  <matrix
                    v-else-if="['matrix', 'Matrix'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :width="item.width"
                    :label-width="item.labelWidth"
                    :data="item.data"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                  />
                  <!-- 应为此处必须添加操作 -->
                  <auto-table
                    v-else-if="['autoTable', 'AutoTable'].includes(item.type)"
                    v-model="customForm[item.prop]"
                    :width="item.width"
                    :table-cols="item.tableCols"
                    :length-min="item.lengthMin"
                    :length-max="item.lengthMax"
                    :disabled="
                      (item.handledisabled &&
                        item.handledisabled(
                          customForm[item.prop],
                          extraData
                        )) ||
                      item.disabled
                    "
                  />

                  <!-- tip 提示语  -->
                  <template>
                    <div
                      v-if="item.tip"
                      style="color: #e6a23c; line-height: 26px; margin: 0"
                    >
                      {{ item.tip }}
                    </div>
                  </template>
                </el-form-item>
              </div>
            </template>
          </el-col>
        </template>
      </el-row>
    </el-form>
  </div>
</template>
<script>
// 级联
import { regionData } from "element-china-area-data";
import UploadImages from "./components/UploadImages";
import SelectTree from "./components/SelectTree";
import RadioGroupInput from "./components/RadioGroupInput";
import CheckboxGroupInput from "./components/CheckboxGroupInput";
import AutoTable from "./components/AutoTable";
import Matrix from "./components/Matrix";
export default {
  name: "BluexiiCustomForm",
  // 懒加载----
  directives: {
    "el-select-lazyloading": {
      bind(el, binding) {
        const SELECT_DOM = el.querySelector(
          ".el-select-dropdown .el-select-dropdown__wrap"
        );
        SELECT_DOM.addEventListener("scroll", function () {
          const condition =
            this.scrollHeight - this.scrollTop <= this.clientHeight;
          if (condition) {
            binding.value();
          }
        });
      },
    },
  },
  components: {
    UploadImages,
    SelectTree,
    RadioGroupInput,
    CheckboxGroupInput,
    Matrix,
    AutoTable,
  },
  props: {
    formConfig: {
      type: Array,
      default: () => [],
    },
    size: {
      type: String,
      default: "small",
    },
    formData: {
      type: Object,
      default: () => {},
    },
    labelPosition: {
      type: String,
      default: "top",
    },
    labelWidth: {
      type: String,
      default: "100px",
    },
    handleUpload: {
      type: Function,
    },
    gutter: {
      type: Number,
      default: 0,
    },
    extraData: {
      type: Object,
      default: () => {},
    },
  },
  data() {
    return {
      regionOptions: regionData,
      formConfigSon: [],
      //   表单数据
      customForm: this.formData,
      customFormrules: [],
      buttonCol: [
        {
          label: "操作",
          type: "Button",
          width: "80",
          fixed: "right",
          name: "option",
          btnList: [
            {
              type: "text",
              icon: "el-icon-circle-plus-outline",
              size: "small",
              style: "color:#FF1600;font-size: 16px",
              handle: () => {},
            },
            {
              type: "text",
              icon: "el-icon-remove-outline",
              size: "small",
              style: "font-size: 16px",
              handle: () => {},
            },
          ],
        },
      ],
    };
  },
  watch: {
    formConfig: {
      handler(newVal) {
        this.formConfigSon = newVal;
        // 添加customFormrules 表单验证
        const textMatch = {
          select: { text: "请选择", trigger: "change" },
          Select: { text: "请选择", trigger: "change" },
          datetimePicker: { text: "请选择", trigger: "change" },
          DatetimePicker: { text: "请选择", trigger: "change" },
          datePicker: { text: "请选择", trigger: "change" },
          DatePicker: { text: "请选择", trigger: "change" },
          image: { text: "请选择", trigger: "change" },
          Image: { text: "请选择", trigger: "change" },
        };
        newVal.forEach((item) => {
          // 如果item的rules存在，那么优先级最大
          if (item.regular) {
            this.customFormrules[item.prop] = [
              {
                required: item.required || false,
                message:
                  item.message ||
                  `${
                    textMatch[item.type]
                      ? textMatch[item.type]["text"]
                      : "请输入"
                  }${item.name}`,
                trigger: textMatch[item.type]
                  ? textMatch[item.type]["trigger"]
                  : "blur",
              },
              {
                pattern: new RegExp(item.regular),
                message: "不符合格式",
                trigger: textMatch[item.type]
                  ? textMatch[item.type]["trigger"]
                  : "blur",
              },
            ];
          } else {
            this.customFormrules[item.prop] = [
              {
                required: item.required || false,
                message:
                  item.message ||
                  `${
                    textMatch[item.type]
                      ? textMatch[item.type]["text"]
                      : "请输入"
                  }${item.name}`,
                trigger: textMatch[item.type]
                  ? textMatch[item.type]["trigger"]
                  : "blur",
              },
            ];
          }
        });
      },
      immediate: true,
    },
    customForm: {
      handler(newVal) {
        this.$emit("update:formData", newVal);
      },
      deep: true,
    },
  },
  mounted() {
    this.monitoring(); // 注册监听事件
  },
  methods: {
    // 表单控制--------------------------------------------
    monitoring() {
      this.$on("verify", (res) => {
        this.verifySon("ruleCustomForm", res);
      });
      this.$on("resetFields", (res) => {
        this.resetFieldsSon("ruleCustomForm", res);
      });
    },
    // 验证表单
    verifySon(formName, callback) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          callback();
        } else {
          this.$message.error("表单信息填写有误！");
          return false;
        }
      });
    },
    // 表单重置
    resetFieldsSon(formName, initialForm) {
      this.$refs[formName].resetFields();
      this.customForm = Object.assign({}, initialForm);
    },
    // -------------------------
    // 验证表单  directive
    verify(callback) {
      return new Promise((res, rej) => {
        this.$refs["ruleCustomForm"].validate((valid) => {
          if (valid) {
            res(true);
            callback();
          } else {
            rej(false);
          }
        });
      });
    },
    // 表单重置 directive
    resetFields(initialForm) {
      this.$refs["ruleCustomForm"].resetFields();
      this.customForm = Object.assign({}, initialForm);
    },
    // --------------------------------------------
    handleInput(value, pattern, fn, field) {
      value = value.replace(pattern, "");
      this.customForm[field] = value;
      fn;
    },
  },
};
</script>
<style lang="scss" scoped>
.footer {
  margin-top: 10px;
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.radio-group-style {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
}
::v-deep .input-style .el-input__inner {
  border-radius: 0px;
  border-top-width: 0px;
  border-left-width: 0px;
  border-right-width: 0px;
  border-bottom-width: 0px;
}

// .form-item {
//   min-width: 100%;
//   height: 100%;
// }
</style>
