<template>
  <section :class="`${tagClass} sr-select`" :style="`width:${width}`" >
    <div :class="`${tagClass} sr-select-input text-truncate ${ (isShowEdit || isEditing) ? 'edit' : ''} ${currentComparedValue ? 'compared' : ''}`" :title="currentValue" @mouseenter="handleMouseEnter" @mouseleave.stop="handleMouseLeave">
      <span v-if="!isShowEdit && !isEditing">
        <span v-if="currentValue" class="user-input-value" v-text="currentValue"></span>
        <span v-if="currentSign && currentComparedValue !== '' && this.currentValue !== ''" v-text="sign"></span>
        <span v-if="currentComparedValue" v-text="currentComparedValue"></span>
        <span v-if="currentValue === '' && currentComparedValue === ''" v-text="defaultValue"></span>
      </span>
      <input
        v-if="isShowEdit || isEditing"
        v-model="currentValue"
        ref="refInput"
        class="input"
        type="text"
        :readOnly="!enterable"
        @focus="handleFocus"
        @blur="handleBlur"
        :placeholder="$t('select.select_please')"
      />
      <i v-if="isShowEdit || isEditing" :class="isClose ? 'r-icon-arrow-down' : 'r-icon-arrow-up'" @click="handleClick"></i>
    </div>
    <options
      ref="refOptions"
      v-model="currentValue"
      :visible="isShowOptions"
      :style="optionsStyle"
      :options="currentOptions"
      :tagClass="tagClass"
      :width="width"
      @selected="handleSelected"
    ></options>
  </section>
</template>

<script lang="ts" src="./index.ts"></script>
