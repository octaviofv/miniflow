<template>
  <div class="flowchart-container" :style="containerStyle">
    <VueFlow
      v-if="initialized"
      v-model="elements"
      :default-zoom="defaultZoom"
      :min-zoom="minZoom"
      :max-zoom="maxZoom"
      :fit-view-on-init="true"
      :nodes-draggable="!isEditing"
      :nodes-connectable="!isEditing"
      :elements-selectable="!isEditing"
      class="flowchart"
      @nodeClick="onNodeClick"
      @connect="onConnect"
      @paneClick="onPaneClick"
    >
      <template #node-custom="nodeProps">
        <CustomNode v-bind="nodeProps" />
      </template>

      <Background :pattern-color="backgroundColor" :gap="backgroundGap" />
      <Controls />
      <MiniMap v-if="showMinimap" />

      <Panel position="top-right" v-if="!isEditing">
        <button class="control-button" @click="addNode">Add Node</button>
        <!-- <button class="control-button" @click="deleteSelected">Delete Selected</button> -->
      </Panel>
    </VueFlow>
  </div>
</template>

<script>
import { ref, computed, watch, onMounted } from 'vue';
import { 
  VueFlow, 
  useVueFlow,
  Background, 
  Controls, 
  MiniMap, 
  Panel 
} from '@vue-flow/core';
import '@vue-flow/core/dist/style.css';
import '@vue-flow/core/dist/theme-default.css';
import '@vue-flow/controls/dist/style.css';
import '@vue-flow/minimap/dist/style.css';
import CustomNode from './components/CustomNode.vue';

export default {
  name: 'FlowChart',
  components: {
    VueFlow,
    Background,
    Controls,
    MiniMap,
    Panel,
    CustomNode,
  },
  props: {
    content: {
      type: Object,
      required: true,
    },
    uid: {
      type: String,
      required: true,
    },
    /* wwEditor:start */
    wwEditorState: { type: Object, required: true },
    /* wwEditor:end */
  },
  emits: ['trigger-event'],
  setup(props, { emit }) {
    const initialized = ref(false);
    const elements = ref([]);
    const selectedNode = ref(null);

    // Editor state
    const isEditing = computed(() => {
      /* wwEditor:start */
      return props.wwEditorState?.isEditing;
      /* wwEditor:end */
      return false;
    });

    // Initialize VueFlow
    const { findNode, addNodes, addEdges, removeNodes } = useVueFlow({
      defaultEdgeOptions: {
        type: 'smoothstep',
        animated: true,
      },
    });

    // Computed properties
    const containerStyle = computed(() => ({
      height: props.content?.height || '600px',
      backgroundColor: props.content?.backgroundColor || '#f5f5f5',
    }));

    const defaultZoom = computed(() => props.content?.defaultZoom || 1);
    const minZoom = computed(() => props.content?.minZoom || 0.1);
    const maxZoom = computed(() => props.content?.maxZoom || 4);
    const backgroundGap = computed(() => props.content?.backgroundGap || 20);
    const showMinimap = computed(() => props.content?.showMinimap ?? true);
    const backgroundColor = computed(() => props.content?.backgroundColor || '#f5f5f5');

    // Initialize flow data
    onMounted(() => {
      try {
        if (props.content?.flowData) {
          const parsedData = typeof props.content.flowData === 'string' 
            ? JSON.parse(props.content.flowData) 
            : props.content.flowData;
          
          elements.value = [
            ...(parsedData.nodes || []),
            ...(parsedData.edges || [])
          ];
        }
        initialized.value = true;
      } catch (error) {
        console.error('Error initializing flow data:', error);
        elements.value = [];
        initialized.value = true;
      }
    });

    // Watch for flow data changes
    watch(() => props.content?.flowData, (newData) => {
      if (newData && initialized.value) {
        try {
          const parsedData = typeof newData === 'string' ? JSON.parse(newData) : newData;
          elements.value = [
            ...(parsedData.nodes || []),
            ...(parsedData.edges || [])
          ];
        } catch (error) {
          console.error('Invalid flow data:', error);
        }
      }
    });

    // Methods
    const generateId = () => `node_${Date.now()}`;

    const addNode = () => {
      const newNode = {
        id: generateId(),
        type: 'custom',
        position: { x: 100, y: 100 },
        data: {
          label: 'New Node',
          content: 'Node content',
          backgroundColor: '#ffffff',
          headerColor: '#4CAF50',
        },
      };

      addNodes([newNode]);
      emit('trigger-event', { name: 'nodeAdded', event: { node: newNode } });
    };

    const onNodeClick = (event, node) => {
      selectedNode.value = node;
      console.log('Nodo seleccionado:', node); // Verifica que el nodo seleccionado se registre correctamente
      emit('trigger-event', { name: 'nodeSelected', event: { node } });
    };

    const onConnect = (connection) => {
      if (connection?.source && connection?.target) {
        const newEdge = {
          id: `edge-${connection.source}-${connection.target}`,
          ...connection,
          type: 'smoothstep',
          animated: true,
        };
        
        addEdges([newEdge]);
        emit('trigger-event', { name: 'connectionCreated', event: { connection: newEdge } });
      }
    };

    const onPaneClick = () => {
      selectedNode.value = null;
      emit('trigger-event', { name: 'selectionCleared' });
    };

    const deleteSelected = () => {
      if (selectedNode.value) {
        console.log('Intentando eliminar nodo:', selectedNode.value.id);

        // Eliminar el nodo seleccionado por su ID
        removeNodes([selectedNode.value.id]);

        console.log('Nodo eliminado con éxito.');
        selectedNode.value = null;

        // Emitir el evento indicando que el nodo fue eliminado
        emit('trigger-event', { name: 'nodeDeleted' });
      } else {
        console.warn('No hay un nodo seleccionado para eliminar.');
      }
    };

    const updateNodeData = (nodeId, data) => {
      const node = findNode(nodeId);
      if (node) {
        node.data = { ...node.data, ...data };
        emit('trigger-event', { name: 'nodeUpdated', event: { node } });
      }
    };

    return {
      elements,
      initialized,
      isEditing,
      containerStyle,
      defaultZoom,
      minZoom,
      maxZoom,
      backgroundGap,
      showMinimap,
      backgroundColor,
      addNode,
      onNodeClick,
      onConnect,
      onPaneClick,
      deleteSelected,
      updateNodeData,
    };
  },
};
</script>

<style lang="scss" scoped>
.flowchart-container {
  width: 100%;
  position: relative;
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;

  :deep(.vue-flow) {
    width: 100%;
    height: 100%;
  }

  :deep(.vue-flow__node) {
    width: auto;
    height: auto;
  }

  :deep(.vue-flow__handle) {
    width: 8px;
    height: 8px;
    background: #2196F3;
    border: 2px solid white;
  }

  :deep(.vue-flow__edge-path) {
    stroke: #2196F3;
    stroke-width: 2;
  }
}

.control-button {
  margin: 4px;
  padding: 8px 16px;
  background-color: #2196F3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;

  &:hover {
    background-color: #1976D2;
  }
}
</style>
