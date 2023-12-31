<template>
  <transition
    name="dialog-fade"
    @after-enter="afterEnter"
    @after-leave="afterLeave">
    <div
      v-show="visible"
      class="r-dialog__wrapper flex-center"
      @mousedown="handleWrapperClick($event, 'down')"
      @mouseup.self="handleWrapperClick($event)">
      <div
        role="dialog"
        :key="key"
        aria-modal="true"
        :aria-label="title || 'dialog'"
        :class="['r-dialog', { 'is-fullscreen': fullscreen, 'r-dialog--center': center }, customClass]"
        ref="dialog"
        :style="style">
        <div class="r-dialog__header">
          <slot name="title">
            <span class="r-dialog__title">{{ title }}</span>
          </slot>
          <button
            type="button"
            class="r-dialog__headerbtn"
            aria-label="Close"
            v-if="showClose"
            @click="handleClose">
            <i class="r-dialog__close r-icon r-icon-close"></i>
          </button>
        </div>
        <div class="r-dialog__body clearfix" v-if="rendered"><slot></slot></div>
        <div class="r-dialog__footer" v-if="$slots.footer">
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import Popup from '@/components/RSrc/utils/popup';
import Migrating from '@/components/RSrc/mixins/migrating';
import emitter from '@/components/RSrc/mixins/emitter';

export default {
  name: 'RDialog',

  mixins: [Popup, emitter, Migrating],

  props: {
    title: {
      type: String,
      default: ''
    },

    modal: {
      type: Boolean,
      default: true
    },

    modalAppendToBody: {
      type: Boolean,
      default: true
    },

    appendToBody: {
      type: Boolean,
      default: false
    },

    lockScroll: {
      type: Boolean,
      default: true
    },

    closeOnClickModal: {
      type: Boolean,
      default: true
    },

    closeOnPressEscape: {
      type: Boolean,
      default: true
    },

    showClose: {
      type: Boolean,
      default: true
    },

    width: String,

    fullscreen: Boolean,

    customClass: {
      type: String,
      default: ''
    },

    top: {
      type: String,
      default: '15vh'
    },
    beforeClose: Function,
    center: {
      type: Boolean,
      default: false
    },

    destroyOnClose: Boolean
  },

  data() {
    return {
      closed: false,
      key: 0,
      clickDom: null
    };
  },

  watch: {
    visible(val) {
      if (val) {
        this.closed = false;
        this.$emit('open');
        this.$el.addEventListener('scroll', this.updatePopper);
        this.$nextTick(() => {
          this.$refs.dialog.scrollTop = 0;
        });
        if (this.appendToBody) {
          document.body.appendChild(this.$el);
        }
      } else {
        this.$el.removeEventListener('scroll', this.updatePopper);
        if (!this.closed) this.$emit('close');
        if (this.destroyOnClose) {
          this.$nextTick(() => {
            this.key++;
          });
        }
      }
    }
  },

  computed: {
    style() {
      const style = {};
      if (!this.fullscreen) {
        // style.marginTop = this.top;
        if (this.width) {
          style.width = this.width;
        }
      }
      return style;
    }
  },

  methods: {
    getMigratingConfig() {
      return {
        props: {
          'size': 'size is removed.'
        }
      };
    },

    /**
     * 关闭弹框
     * e event
     * clickType
     */
    handleWrapperClick(e, clickType) {
      if (!this.closeOnClickModal) return;
      if (clickType === 'down') {
        this.clickDom = e.target;
      } else {
        if (this.clickDom && this.clickDom.classList.contains('r-dialog__wrapper') && e.target.classList.contains('r-dialog__wrapper')) {
          this.handleClose();
        }
      }
    },
    handleClose() {
      if (typeof this.beforeClose === 'function') {
        this.beforeClose(this.hide);
      } else {
        this.hide();
      }
    },
    hide(cancel) {
      if (cancel !== false) {
        this.$emit('update:visible', false);
        this.$emit('close');
        this.closed = true;
      }
    },
    updatePopper() {
      this.broadcast('ElSelectDropdown', 'updatePopper');
      this.broadcast('ElDropdownMenu', 'updatePopper');
    },
    afterEnter() {
      this.$emit('opened');
    },
    afterLeave() {
      this.$emit('closed');
    }
  },

  mounted() {
    if (this.visible) {
      this.rendered = true;
      this.open();
      if (this.appendToBody) {
        document.body.appendChild(this.$el);
      }
    }
  },

  destroyed() {
    // if appendToBody is true, remove DOM node after destroy
    if (this.appendToBody && this.$el && this.$el.parentNode) {
      this.$el.parentNode.removeChild(this.$el);
    }
  }
};
</script>
