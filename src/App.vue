<template>
  <div style="width: 100vh; height: 100vh; display:  flex; flex-direction: row;">
    <div v-if="!!zctx" style="flex: 0 0 300px; background-color: lightgray;">
      <div>
        <b>Sidebar Elements</b>
      </div>
      <button
          :disabled="zctx.getCurrentTool() === 'rectangle'"
          @click="zctx.useRectangleTool()"
      >
        Rectangle
      </button>
      <button
          :disabled="zctx.getCurrentTool() === 'brush'"
          @click="zctx.useBrushTool()"
      >
        Brush
      </button>
      <button
          :disabled="zctx.getCurrentTool() === 'text'"
          @click="zctx.useTextTool()"
      >
        Font
      </button>
    </div>
    <div style="flex: 1 0 auto; position: relative;">
      <zwibbler ref="zwibblerContainer" z-no-auto-init class="full flex-col">
        <div z-canvas class="stretch"></div>
      </zwibbler>
    </div>
  </div>
</template>

<script>
// uncomment when testing markRaw
// import { markRaw } from 'vue'

export default {
  name: 'App',
  data () {
    return {
      leftDrawerOpen: false,
      zctx: null
    }
  },
  methods: {
    toggleLeftDrawer () {
      this.leftDrawerOpen = !this.leftDrawerOpen
    }
  },
  mounted () {
    const scope = window.Zwibbler.attach(this.$refs.zwibblerContainer, {})
    this.zctx = scope.ctx
    // uncomment when testing markRaw (and comment above)
    // this.zctx = markRaw(scope.ctx)
  },
  beforeUnmount () {
    if (this.zctx) {
      console.log('DESTROY ZWIBBLER')
      this.zctx.destroy()
      this.zctx = null
    }
  }
}
</script>

<style>
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  margin: 0;
  padding: 0;
}
</style>
