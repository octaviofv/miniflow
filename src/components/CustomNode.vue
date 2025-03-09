<template>
  <div 
    class="note-node" 
    :class="{ selected: selected }"
    :style="{ backgroundColor: data.backgroundColor || '#fff9c4' }"
  >
    <div class="note-header" :style="{ backgroundColor: data.headerColor || '#f57f17' }">
      <span class="note-title">{{ data.label || 'Note' }}</span>
      <div class="note-fold"></div>
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
  border-radius: 3px;
  min-width: 200px;
  min-height: 120px;
  position: relative;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
  
  &.selected {
    box-shadow: 0 0 0 2px #2196F3;
  }

  &:hover {
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  }
}

.note-header {
  padding: 8px 12px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  color: white;
  position: relative;
  display: flex;
  align-items: center;
}

.note-title {
  font-weight: 600;
  font-size: 14px;
  flex-grow: 1;
}

.note-fold {
  position: absolute;
  top: 0;
  right: 0;
  width: 15px;
  height: 15px;
  background: linear-gradient(
    135deg,
    transparent 50%,
    rgba(0, 0, 0, 0.2) 50%
  );
  border-top-right-radius: 3px;
}

.note-content {
  padding: 12px;
  min-height: 80px;
  font-size: 14px;
  line-height: 1.4;
}

.note-textarea {
  width: 100%;
  min-height: 80px;
  border: none;
  background: transparent;
  resize: none;
  font-family: inherit;
  font-size: 14px;
  line-height: 1.4;
  padding: 0;
  color: inherit;

  &:focus {
    outline: none;
  }

  &::placeholder {
    color: rgba(0, 0, 0, 0.3);
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
    width: 8px;
    height: 8px;
    background: #f57f17;
    border: 2px solid white;
    border-radius: 50%;
    transition: transform 0.2s ease;

    &:hover {
      transform: scale(1.2);
    }
  }
}
</style>
