<template>
  <div class="r-collapse" role="tablist" aria-multiselectable="true">
    <slot></slot>
  </div>
</template>
<script>
export default {
  name: 'RCollapse',

  componentName: 'RCollapse',

  props: {
    accordion: Boolean,
    value: {
      type: [Array, String, Number],
      default() {
        return [];
      }
    }
  },

  data() {
    return {
      activeNames: [].concat(this.value)
    };
  },

  provide() {
    return {
      collapse: this
    };
  },

  watch: {
    value(value) {
      this.activeNames = [].concat(value);
    }
  },

  methods: {
    setActiveNames(activeNames) {
      activeNames = [].concat(activeNames);
      const value = this.accordion ? activeNames[0] : activeNames;
      this.activeNames = activeNames;
      this.$emit('input', value);
      this.$emit('change', value);
    },
    handleItemClick(item) {
      if (this.accordion) {
        this.setActiveNames(
          (this.activeNames[0] || this.activeNames[0] === 0) &&
            this.activeNames[0] === item.name
            ? '' : item.name
        );
      } else {
        const activeNames = this.activeNames.slice(0);
        const index = activeNames.indexOf(item.name);

        if (index > -1) {
          activeNames.splice(index, 1);
        } else {
          activeNames.push(item.name);
        }
        this.setActiveNames(activeNames);
      }
    }
  },

  created() {
    this.$on('item-click', this.handleItemClick);
  }
};
</script>

