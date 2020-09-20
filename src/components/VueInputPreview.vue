<template>
  <section
    class="input-preview-container"
    :style="`height: ${height}; width: ${width};`"
  >
    <div
      class="close"
      @click.self="removeFile()"
    >
      &times;
    </div>

    <label
      class="input-label"
      :for="uniqueId"
      ref="inputFileLabel"
    >
      <section class="hover-label">
        Click to Upload
      </section>

      <input
        v-bind="$attrs"
        :id="uniqueId"
        class="input"
        type="file"
        @input="inputImage($event)"
      >
    </label>
  </section>
</template>

<script>
export default {
  data() {
    return {
      uniqueId: null
    }
  },

  props: {
    value: {
      default: null
    },

    height: {
      type: String,
      default: '100%'
    },

    width: {
      type: String,
      default: '100%'
    }
  },

  watch: {
    value(newFile) {
      this.setImagePreview(newFile)
    }
  },

  created () {
    this.generateUniqueId()
  },

  methods: {
    generateUniqueId () {
      this.uniqueId = Math.random().toString(36).substr(2, 9)
    },

    setImagePreview (file) {
      if (!file) {
        this.$refs.inputFileLabel.style.backgroundImage = ''
        return
      }

      const reader = new FileReader()
      reader.onloadend = () => {
        this.$refs.inputFileLabel.style.backgroundImage = `url(${ reader.result })`
      }

      if (reader) reader.readAsDataURL(file)
    },

    inputImage (inputEvent) {
      const isFileValid = this.isFileValid(inputEvent)
      if (!isFileValid) return
      this.$emit('input', inputEvent.target.files[0])
    },

    isFileValid (inputEvent) {
      const fileType = inputEvent.target.files[0].type.split('/')[1]
      const inputRequiredFileType = inputEvent.target.accept.split('/')[1]

      if (fileType !== inputRequiredFileType && inputRequiredFileType !== '') {
          this.$emit('onError', {
            message: `Please upload (a/an) ${inputRequiredFileType} file type!`,
            requiredType: inputRequiredFileType,
            insertedType: fileType
          })

          return false
      }
      return true
    },

    removeFile () {
      this.$emit('input', null)
    }
  },
}
</script>

<style lang="scss" scoped>
.input-preview-container {
  position: relative;

  .close {
    position: absolute;
    top: -10px;
    right: -8px;
    width: 22px;
    height: 22px;
    padding: 2px 0 0 0;
    font-weight: 600;
    cursor: pointer;
    background: #eb573f;
    color: #fff;
    border-radius: 100%;
    transition: 0.3s;
    z-index: 1;
    display: flex;
    align-items: center;
    justify-content: center;

    &:hover {
      background: darken(#eb573f, 5%);
    }
  }

  .input-label {
    position: relative;
    width: 100%;
    height: 100%;
    padding: 15px;
    display: block;
    cursor: pointer;
    background-size: contain;
    background-position: center;
    background-repeat: no-repeat;
    background-color: #dddddd;

    .hover-label {
      top: 0;
      left: 0;
      position: absolute;
      width: 100%;
      height: 100%;
      font-size: 18px;
      color: #555;
      border: 1px dashed #555;
      transition: 0.3s;
      background-color: rgba(0, 0, 0, 0.2);
      display: flex;
      align-items: center;
      justify-content: center;
      pointer-events: none;
      opacity: 0.5;
    }

    &:hover {
      .hover-label {
        opacity: 1;
      }
    }
    
    .input {
      opacity: 0;
      position: absolute;
      pointer-events: none;
      left: 0;
      top: 0;
    }
  }
}
</style>