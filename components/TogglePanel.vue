<template>
  <div class="toggle-panel" @click="handleToggle" :class="{ 'is-open': isOpen }">
    <div class="toggle-panel-header">
      <div class="toggle-panel-title">{{ title }}</div>
      <div class="toggle-panel-summary">{{ summary }}</div>
      <div class="toggle-panel-arrow">
        <svg viewBox="0 0 24 24" width="20" height="20">
          <path d="M7 10l5 5 5-5z"/>
        </svg>
      </div>
    </div>
    <div class="toggle-panel-content" ref="contentRef">
      <div class="toggle-panel-content-inner">
        <slot />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      required: true
    },
    summary: {
      type: String,
      required: true
    },
    defaultOpen: {
      type: Boolean,
      default: false
    },
    onToggle: {
      type: Function,
      default: () => {}
    }
  },
  data() {
    return {
      isOpen: this.defaultOpen,
      contentHeight: 0,
      isAnimating: false
    };
  },
  computed: {
    contentStyle() {
      return {
        height: `${this.contentHeight}px`,
        transition: 'height 300ms ease'
      };
    }
  },
  watch: {
    isOpen(newVal) {
      this.updateContentHeight();
      this.onToggle(newVal);
    }
  },
  mounted() {
    this.updateContentHeight();
    window.addEventListener('resize', this.updateContentHeight);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.updateContentHeight);
  },
  methods: {
    handleToggle() {
      if (this.isAnimating) return;
      this.isOpen = !this.isOpen;
    },
    updateContentHeight() {
      if (!this.$refs.contentRef) return;
      
      const contentInner = this.$refs.contentRef.querySelector('.toggle-panel-content-inner');
      if (!contentInner) return;
      
      this.contentHeight = this.isOpen ? contentInner.scrollHeight : 0;
      
      // Handle animation state
      if (this.isOpen) {
        this.isAnimating = true;
        setTimeout(() => {
          this.isAnimating = false;
        }, 300);
      } else {
        this.isAnimating = true;
        setTimeout(() => {
          this.isAnimating = false;
        }, 300);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.toggle-panel {
  width: 100%;
  background-color: #fff;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  transition: all 0.3s ease;
  
  &:hover {
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  }
  
  &.is-open {
    .toggle-panel-arrow {
      transform: rotate(90deg);
    }
  }
}

.toggle-panel-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  min-height: 48px;
  
  .toggle-panel-title {
    font-size: 16px;
    font-weight: 600;
    color: #333;
    margin-bottom: 4px;
  }
  
  .toggle-panel-summary {
    font-size: 14px;
    color: #666;
    margin-bottom: 8px;
  }
  
  .toggle-panel-arrow {
    transition: transform 0.3s ease;
    svg {
      fill: #999;
    }
  }
}

.toggle-panel-content {
  overflow: hidden;
  transition: height 300ms ease;
  
  .toggle-panel-content-inner {
    padding-top: 16px;
    font-size: 14px;
    color: #333;
    line-height: 1.6;
  }
}
</style>