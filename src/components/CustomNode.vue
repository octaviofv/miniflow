<template>
  <div
    v-if="data.type === 'circle'"
    class="custom-node circle-node"
    :class="{ selected: selected }"
    :style="{ backgroundColor: data.backgroundColor || '#2196F3' }"
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

/* Nodo circular */
.circle-node {
  width: 100px; /* El ancho del círculo */
  height: 100px; /* El alto del círculo (debe ser igual que el ancho) */
  border-radius: 50%; /* Hacerlo circular */
  display: flex; /* Para centrar contenido */
  align-items: center; /* Centrado vertical */
  justify-content: center; /* Centrado horizontal */
  text-align: center; /* Asegurar texto centrado */
  font-size: 14px; /* Tamaño del texto */
  font-weight: bold; /* Texto en negrita */
  color: white; /* Texto blanco */
  background-color: #2196F3; /* Fondo azul */
  border: 3px solid #1565C0; /* Borde azul oscuro */
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Sombra para destacar */
  position: relative;

  &.selected {
    border: 3px solid #0D47A1; /* Borde más oscuro cuando está seleccionado */
  }
}

/* Estilo del texto interno del nodo circular */
.circle-label {
  text-align: center;
  padding: 8px;
  font-size: 14px;
  font-weight: bold;
  user-select: none; /* Previene selección del texto */
}

/* Estilo de los nodos rectangulares */
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
