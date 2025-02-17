<template>
  <div 
    class="custom-node" 
    :class="{ selected: selected }"
    :style="{ backgroundColor: data.backgroundColor || '#ffffff' }"
  >
    <div class="node-header" :style="{ backgroundColor: data.headerColor || '#4CAF50' }">
      <span class="node-title">{{ data.label || 'Node' }}</span>
    </div>
    <div class="node-content">
      {{ data.content || '' }}
    </div>
    <div class="node-handles">
      <Handle
        v-for="handle in handles"
        :key="handle.id"
        :id="handle.id"
        :type="handle.type"
        :position="handle.position"
      />
    </div>
  </div>
</template>

<script>
import { Handle } from '@vue-flow/core';

export default {
  name: 'CustomNode',
  components: {
    Handle,
  },
  props: {
    id: {
      type: String,
      required: true,
    },
    data: {
      type: Object,
      required: true,
    },
    selected: {
      type: Boolean,
      default: false,
    },
  },
  setup() {
    const handles = [
      //{ id: 'top', type: 'target', position: 'top' },
      //{ id: 'right', type: 'source', position: 'right' },
      { id: 'bottom', type: 'source', position: 'bottom' },
      //{ id: 'left', type: 'target', position: 'left' },
    ];

    return {
      handles,
    };
  },
};
</script>

<style lang="scss" scoped>
.custom-node {
  border-radius: 8px;
  border: 1px solid #ddd;
  min-width: 150px;
  min-height: 80px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  background-color: white;
  
  &.selected {
    border: 2px solid #2196F3;
  }
}

.node-header {
  padding: 8px;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
  color: white;
}

.node-title {
  font-weight: bold;
  font-size: 14px;
}

.node-content {
  padding: 12px;
  font-size: 12px;
}

.node-handles {
  position: relative;
}
</style>
