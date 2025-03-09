<template>
  <div 
    class="note-node" 
    :class="{ selected: selected }"
    :style="{ backgroundColor: data.backgroundColor || '#ffffff' }"
  >
    <div class="note-header" :style="{ backgroundColor: data.headerColor || '#e0e0e0' }">
      <span class="note-title">{{ data.label || 'Note' }}</span>
    </div>
    <div class="note-content">
      <textarea
        v-if="!isReadOnly"
        v-model="localContent"
        class="note-textarea"
        @blur="updateContent"
        @keydown.stop
        placeholder="Write your note here..."
      ></textarea>
      <div v-else class="note-text">{{ data.content || '' }}</div>
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
import { ref, watch } from 'vue';

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
    isReadOnly: {
      type: Boolean,
      default: true,
    }
  },
  emits: ['update:data'],
  setup(props, { emit }) {
    const localContent = ref(props.data.content || '');

    watch(() => props.data.content, (newContent) => {
      localContent.value = newContent || '';
    });

    const handles = [
      { id: 'top', type: 'target', position: 'top' },
      { id: 'right', type: 'source', position: 'right' },
      { id: 'bottom', type: 'source', position: 'bottom' },
      { id: 'left', type: 'target', position: 'left' },
    ];

    const updateContent = () => {
      emit('update:data', {
        ...props.data,
        content: localContent.value
      });
    };

    return {
      handles,
      localContent,
      updateContent
    };
  },
};
</script>

<style lang="scss" scoped>
.note-node {
  border: 1px solid #e0e0e0;
  border-radius: 2px;
  min-width: 180px;
  min-height: 100px;
  position: relative;
  transition: all 0.2s ease;
  background-color: #ffffff;
  
  &.selected {
    border-color: #2196F3;
  }
}

.note-header {
  padding: 8px 12px;
  border-bottom: 1px solid #e0e0e0;
  color: #424242;
}

.note-title {
  font-weight: 500;
  font-size: 13px;
  letter-spacing: 0.3px;
}

.note-content {
  padding: 12px;
  min-height: 60px;
  font-size: 13px;
  line-height: 1.4;
  color: #616161;
}

.note-textarea {
  width: 100%;
  min-height: 60px;
  border: none;
  background: transparent;
  resize: none;
  font-family: inherit;
  font-size: 13px;
  line-height: 1.4;
  padding: 0;
  color: #616161;

  &:focus {
    outline: none;
  }

  &::placeholder {
    color: #9e9e9e;
  }
}

.note-text {
  white-space: pre-wrap;
  word-break: break-word;
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
    width: 6px;
    height: 6px;
    background: #90a4ae;
    border: 1px solid #ffffff;
    border-radius: 50%;
    transition: all 0.2s ease;

    &:hover {
      background: #607d8b;
      transform: scale(1.2);
    }
  }
}
</style>
