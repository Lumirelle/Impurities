<script>
import ImageViewerPanel from './ImageViewerPanel.vue'

/**
 * Image Viewer
 *
 * @example
 * Just wrap the rich text content with `ImageViewer` component, and the images will be displayed in a modal when clicked.
 *
 * ```html
 * <ImageViewer>
 *   <!-- Some rich text content rendered by `v-html` and contains images -->
 *   <section class="article-content" v-html="article.content" />
 * </ImageViewer>
 * ```
 */
export default {
  name: 'ImageViewer',

  components: {
    ImageViewerPanel,
  },

  props: {
    scaleRatio: {
      type: Number,
      default: 1.15,
    },
    maxScale: {
      type: Number,
      default: 5,
    },
    minScale: {
      type: Number,
      default: 0.1,
    },
    targetWidth: {
      type: Number,
      default: 600,
    },
    targetHeight: {
      type: Number,
      default: 400,
    },
  },

  data() {
    return {
      panelVisible: false,
      images: [],
      currentImageIndex: 0,
    }
  },

  mounted() {
    this.init()
  },
  beforeUnmount() {
    this.cleanup()
  },

  methods: {
    init() {
      // 事件委托：监听整个wrapper的点击事件
      this.$refs.content.addEventListener('click', this.handleImageClick)
    },

    cleanup() {
      this.$refs.content.removeEventListener('click', this.handleImageClick)
    },

    handleImageClick(event) {
      if (event.target.tagName === 'IMG') {
        // 收集所有图片
        const images = Array.from(this.$refs.content.querySelectorAll('img'))
          .map(img => ({
            src: img.src || '',
            alt: img.alt || '',
            title: img.title || img.alt || '',
          }))

        if (images.length === 0)
          return

        // 找到当前图片索引
        const currentIndex = images.findIndex(img => img.src === event.target.src)

        if (currentIndex >= 0) {
          this.openImageViewer(images, currentIndex)
        }
      }
    },

    openImageViewer(images, index) {
      this.images = images
      this.currentImageIndex = index
      this.panelVisible = true
      // 阻止页面滚动
      document.body.style.overflow = 'hidden'
    },

    closeImageViewer() {
      this.panelVisible = false
      // 恢复页面滚动
      document.body.style.overflow = ''
    },

    prevImage() {
      if (this.currentImageIndex > 0) {
        this.currentImageIndex--
      }
    },

    nextImage() {
      if (this.currentImageIndex < this.images.length - 1) {
        this.currentImageIndex++
      }
    },
  },
}
</script>

<template>
  <div class="image-viewer-wrapper">
    <div ref="content" class="image-viewer-wrapper__content">
      <slot />
    </div>
    <ImageViewerPanel
      :visible="panelVisible"
      :images="images"
      :current-index="currentImageIndex"
      :scale-ratio="scaleRatio"
      :max-scale="maxScale"
      :min-scale="minScale"
      :target-width="targetWidth"
      :target-height="targetHeight"
      @close="closeImageViewer"
      @prev="prevImage"
      @next="nextImage"
    />
  </div>
</template>

<style lang="scss" scoped>
.image-viewer-wrapper {
  position: relative;

  &__content {
    :deep(img) {
      cursor: zoom-in;
    }
  }
}
</style>
