<template>
  <li
    @mouseenter="hoverItem"
    @click.stop="selectOptionClick"
    class="r-select-dropdown__item"
    v-show="visible"
    :title="title || currentLabel"
    :style="maxWidth ? `max-width: ${maxWidth}px` : ''"
    :class="{
      'selected': itemSelected,
      'is-disabled': disabled || groupDisabled || limitReached,
      'hover': hover
    }"
    >
    <slot>
      <span class="current-label" :title="title || currentLabel">{{ currentLabel }}</span>
    </slot>
  </li>
</template>

<script type="text/babel">
import Emitter from '@/components/RSrc/mixins/emitter';
import { getValueByPath, escapeRegexpString } from '@/components/RSrc/utils/util';

export default {
  mixins: [Emitter],

  name: 'ROptionEnhance',

  componentName: 'ROptionEnhance',

  inject: ['select'],

  props: {
    value: {
      required: true
    },
    label: [String, Number],
    created: Boolean,
    disabled: {
      type: Boolean,
      default: false
    },
    maxWidth: String,
    title: [String, Number]
  },

  data() {
    return {
      index: -1,
      groupDisabled: false,
      visible: true,
      hitState: false,
      hover: false
    };
  },

  computed: {
    isObject() {
      return Object.prototype.toString.call(this.value).toLowerCase() === '[object object]';
    },

    currentLabel() {
      return this.label || (this.isObject ? '' : this.value);
    },

    currentValue() {
      return this.value || this.label || '';
    },

    itemSelected() {
      if (!this.select.multiple) {
        return this.isEqual(this.value, this.select.value);
      } else {
        return this.contains(this.select.value, this.value);
      }
    },

    limitReached() {
      if (this.select.multiple) {
        return !this.itemSelected &&
          (this.select.value || []).length >= this.select.multipleLimit &&
          this.select.multipleLimit > 0;
      } else {
        return false;
      }
    }
  },

  watch: {
    currentLabel() {
      if (!this.created && !this.select.remote) this.dispatch('RSelectEnhance', 'setSelected');
    },
    value(val, oldVal) {
      const { remote, valueKey } = this.select;
      if (!this.created && !remote) {
        if (valueKey && typeof val === 'object' && typeof oldVal === 'object' && val[valueKey] === oldVal[valueKey]) {
          return;
        }
        this.dispatch('RSelectEnhance', 'setSelected');
      }
    }
  },

  methods: {
    isEqual(a, b) {
      if (!this.isObject) {
        return a === b;
      } else {
        const valueKey = this.select.valueKey;
        return getValueByPath(a, valueKey) === getValueByPath(b, valueKey);
      }
    },

    contains(arr = [], target) {
      if (!this.isObject) {
        return arr && arr.indexOf(target) > -1;
      } else {
        const valueKey = this.select.valueKey;
        return arr && arr.some(item => {
          return getValueByPath(item, valueKey) === getValueByPath(target, valueKey);
        });
      }
    },

    handleGroupDisabled(val) {
      this.groupDisabled = val;
    },

    hoverItem() {
      if (!this.disabled && !this.groupDisabled) {
        this.select.hoverIndex = this.select.options.indexOf(this);
      }
    },

    selectOptionClick() {
      if (this.disabled !== true && this.groupDisabled !== true) {
        this.dispatch('RSelectEnhance', 'handleOptionClick', [this, true]);
      }
    },

    queryChange({ query, isInput }) {
      /** 用于函数选择时搜索只搜函数名，不搜类型，后续可以做成通用的 */
      if (isInput) {
        if (query.indexOf('(') !== -1 || query.indexOf(')') !== -1) {
          this.visible = false; 
        } else {
          const label = query ? this.currentLabel.split('(')[0] : '';
          this.visible = new RegExp(escapeRegexpString(query), 'i').test(label) || this.created;
        } 
      } else {
        this.visible = new RegExp(escapeRegexpString(query), 'i').test(this.currentLabel) || this.created;
      }
      if (!this.visible) {
        this.select.filteredOptionsCount--;
      }
    }
  },

  created() {
    this.select.options.push(this);
    this.select.cachedOptions.push(this);
    this.select.optionsCount++;
    this.select.filteredOptionsCount++;

    this.$on('queryChange', this.queryChange);
    this.$on('handleGroupDisabled', this.handleGroupDisabled);
  },

  beforeDestroy() {
    const { selected, multiple } = this.select;
    const selectedOptions = multiple ? selected : [selected];
    const index = this.select.cachedOptions.indexOf(this);
    const selectedIndex = selectedOptions.indexOf(this);

    // if option is not selected, remove it from cache
    if (index > -1 && selectedIndex < 0) {
      this.select.cachedOptions.splice(index, 1);
    }
    this.select.onOptionDestroy(this.select.options.indexOf(this));
  }
};
</script>
