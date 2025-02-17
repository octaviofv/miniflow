<template>
<div 
  class="circle-node" 
  :class="[`node-type-${type}`, { selected }]"
  :style="computedNodeStyle"
>
  <div class="node-content">
    <i v-if="icon" :class="icon" class="node-icon"></i>
    <span class="node-label">{{ label }}</span>
  </div>
  <div class="node-handles">
    <Handle
      v-if="type === 'event-start'"
      id="right"
      type="source"
      position="right"
    />
    <Handle
      v-if="type === 'event-end'"
      id="left"
      type="target"
      position="left"
    />
  </div>
</div>
</template>

<script>
import { computed } from 'vue';
import { Handle } from '@vue-flow/core';

export default {
  name: 'CircleNode',
  components: {
    Handle
  },
  props: {
    type: {
      type: String,
      required: true,
      validator: value => ['event-start', 'event-end'].includes(value)
    },
    label: {
      type: String,
      default: ''
    },
    icon: {
      type: String,
      default: ''
    },
    selected: {
      type: Boolean,
      default: false
    },
    backgroundColor: {
      type: String,
      default: '#ffffff'
    }
  },
  setup(props) {
    const computedNodeStyle = computed(() => ({
      backgroundColor: props.backgroundColor
    }));

    return {
      computedNodeStyle
    };
  }
};
</script>

<style lang="scss" scoped>
.circle-node {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: 2px solid currentColor;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  transition: all 0.3s ease;

  &.selected {
    border-color: #2196F3;
    box-shadow: 0 0 0 2px rgba(33, 150, 243, 0.2);
  }

  &.node-type-event-start {
    color: #4CAF50;
    border-width: 2px;
  }

  &.node-type-event-end {
    color: #f44336;
    border-width: 2px;
  }

  .node-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
  }

  .node-icon {
    font-size: 16px;
  }

  .node-label {
    font-size: 12px;
    white-space: nowrap;
    position: absolute;
    bottom: -20px;
    left: 50%;
    transform: translateX(-50%);
  }

  .node-handles {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    pointer-events: none;

    :deep(.vue-flow__handle) {
      pointer-events: all;
      width: 8px;
      height: 8px;
      background: currentColor;
      border: 2px solid white;
      transition: all 0.2s ease;

      &:hover {
        transform: scale(1.2);
      }
    }
  }
}
</style>
