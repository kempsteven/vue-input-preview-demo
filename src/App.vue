<template>
  <div id="app">
    <section class="container">
      <vue-input-preview
        @onError="onError($event)"
        :required-file-types="['image']"
        multiple  
        v-model="file"
      />
    </section>
  </div>
</template>

<script>
import VueInputPreview from '@/components/VueInputPreview'
export default {
  components: {
    VueInputPreview,
  },

  data() {
    return {
      file: null
    }
  },

  methods: {
    onError ({ message, requiredType, insertedType }) {
      alert(`
        Message: ${message}
        Required Type: ${requiredType}
        Inserted Type: ${insertedType}
      `)
    }
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 25px;
}

* {
  box-sizing: border-box;
}

html,
body {
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
}

.container {
  width: 50%;
  height: 50%;
}
</style>
