<template>
  <ul class="r-select-group__wrap" v-show="visible">
    <li class="r-select-group__title">{{ label }}</li>
    <li>
      <ul class="r-select-group">
        <slot></slot>
      </ul>
    </li>
  </ul>
</template>

<script type="text/babel">
import Emitter from '@/components/RSrc/mixins/emitter';

export default {
  mixins: [Emitter],

  name: 'ROptionGroup',

  componentName: 'ROptionGroup',

  props: {
    label: String,
    disabled: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      visible: true
    };
  },

  watch: {
    disabled(val) {
      this.broadcast('ROption', 'handleGroupDisabled', val);
    }
  },

  methods: {
    queryChange() {
      this.visible = this.$children &&
        Array.isArray(this.$children) &&
        this.$children.some(option => option.visible === true);
    }
  },

  created() {
    this.$on('queryChange', this.queryChange);
  },

  mounted() {
    if (this.disabled) {
      this.broadcast('ROption', 'handleGroupDisabled', this.disabled);
    }
  }
};
</script>
