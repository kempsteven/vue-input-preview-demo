<template>
  <section
    class="input-preview-container"
    :style="`height: ${height}; width: ${width};`"
  >
    <div
      v-if="value"
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

      <div
        class="inserted-item"
        :key="key"
        :style="`background-image: url(${item})`"
        v-for="(item, key) in insertedItem"/>

      <input
        v-bind="$attrs"
        :id="uniqueId"
        class="input"
        type="file"
        ref="input"
        @input="inputImage($event)"
      >
    </label>
  </section>
</template>

<script>
// import mimeTypes from 'mime-types'
// import mimeDb from 'mime-db'
export default {
  data() {
    return {
      uniqueId: null,
      insertedItem: []
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
    },

    requiredFileTypes: {
      type: Array,
      default: () => {
        return []
      }
    }
  },

  watch: {
    value(files) {
      this.setImagePreview(files)
    }
  },

  created () {
    this.generateUniqueId()
  },

  methods: {
    generateUniqueId () {
      this.uniqueId = Math.random().toString(36).substr(2, 9)
    },

    setImagePreview (files) {
      if (!files || !files.length) {
        this.insertedItem = []
        return
      }


      files.forEach(file => {
        const reader = new FileReader()

        reader.onloadend = () => {
          this.insertedItem.push(reader.result)
        }
  
        if (reader) reader.readAsDataURL(file)
      })
    },

    inputImage (inputEvent) {
      const isFileValid = this.isFileValid(inputEvent)
      if (!isFileValid) return

      const value = []
      const files = inputEvent.target.files
      const fileKeys = Object.keys(files)

      fileKeys.forEach(key => {
        const file = files[key]
        value.push(file)
      })

      this.$emit('input', value)
    },

    isFileValid (inputEvent) {
      const files = inputEvent.target.files
      const fileKeys = Object.keys(files)

      if (!this.requiredFileTypes.length) return true

      for (let index = 0; index < fileKeys.length; index++) {
        const key = fileKeys[index]
        const file = files[key]
        const fileType = file.type

        for (let i = 0; i < this.requiredFileTypes.length; i++) {
          const requiredfileType = this.requiredFileTypes[i]

          if (fileType.indexOf(requiredfileType) === -1) {
            return false
          }
        }
      }

      return true
    },

    removeFile () {
      this.$refs.input.value = null
      this.$refs.input.files = null
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
      color: #fff;
      text-shadow: 0px 2px 5px #555;
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

    .inserted-item {
      width: 100%;
      height: 100%;
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