<template>
  <div
    v-if="data.type === 'circle'"
    class="custom-node circle-node"
    :class="{ selected: selected }"
    :style="{ backgroundColor: data.backgroundColor || '#ffffff' }"
  >
    <!-- Contenido del nodo circular -->
    <div class="circle-label">
      {{ data.label || 'Start/End' }}
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
  <div 
    v-else
    class="custom-node" 
    :class="{ selected: selected }"
    :style="{ backgroundColor: data.backgroundColor || '#ffffff' }"
  >
    <!-- Contenido del nodo estándar (rectangular) -->
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
  setup(props) {
    // Define diferentes arreglos de handles dependiendo del tipo de nodo
    const handles = props.data.type === 'circle'
      ? [
          { id: 'bottom', type: 'source', position: 'bottom' }, // Solo salida en el nodo circular
        ]
      : [
          { id: 'top', type: 'source', position: 'top' },
          { id: 'bottom', type: 'source', position: 'bottom' },
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
  position: relative;

  &.selected {
    border: 2px solid #2196F3;
  }
}

.circle-node {
  width: 100px;
  height: 100px;
  border-radius: 50%; /* Nodo en forma de círculo */
  border: 1px solid #ddd;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  font-size: 12px;
  font-weight: bold;
  color: #333;
  background-color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  position: relative;

  &.selected {
    border: 2px solid #2196F3;
  }
}

.circle-label {
  text-align: center;
  padding: 8px;
  font-size: 14px;
  font-weight: bold;
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
  position: absolute;
}
</style>
