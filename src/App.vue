<template>
  <div style="width: 100vw; height: 100vh; display:  flex; flex-direction: row;">
    <div v-if="!!zctx" style="flex: 0 0 300px; background-color: lightgray;">
      <div>
        <b>Sidebar Elements</b>
      </div>
      <div style="display: flex;">
        <button :disabled="zctx.getCurrentTool() === 'rectangle'" @click="zctx.useRectangleTool()">
          Rectangle
        </button>
        <button :disabled="zctx.getCurrentTool() === 'brush'" @click="zctx.useBrushTool()">
          Brush
        </button>
        <button :disabled="zctx.getCurrentTool() === 'text'" @click="zctx.useTextTool()">
          Font
        </button>
        <button @click="setBackground()">Set background image</button>

        <!-- paste an image using Ctrl-V or drag from your desktop to demonstrate this. -->
        <button @click="zctx.flip(90)" v-if="summary.types.ImageNode">Flip</button>
        <button @click="zctx.useEditHandleTool(zctx.getSelectedNodes()[0])" v-if="summary.types.ImageNode">Crop</button>
      </div>
      <select v-if="'fontSize' in summary.properties" v-model="fontSize">
        <option>6</option>
        <option>10</option>
        <option>15</option>
        <option>20</option>
        <option>25</option>
      </select>
    </div>
    <zwibbler ref="zwibblerContainer" z-no-auto-init>
      <div z-canvas></div>
    </zwibbler>
  </div>
</template>

<script>
// uncomment when testing markRaw
import { markRaw } from 'vue'
import './zwibbler-demo.js'

export default {
  name: 'App',
  data() {
    return {
      leftDrawerOpen: false,
      zctx: null,

      // This contains information about selected items that you will want to reactively display things like
      // a font size selector, etc. See https://zwibbler.com/docs/#getpropertysummary
      // eg. <select v-if="'fontSize' in summary.properties">
      summary: {
        properties: {},
        types: {},
        empty: true,
      }
    };
  },
  methods: {
    toggleLeftDrawer() {
      this.leftDrawerOpen = !this.leftDrawerOpen
    },

    setBackground() {
      this.zctx.createNode("ImageNode", {
        url: "https://placekitten.com/800/600",
        layer: "background",
        zIndex: -1,
      })
    }
  },

  computed: {
    fontSize: {
      get() {
        return this.summary.properties.fontSize
      },
      set(value) {
        console.log("Set fontsize to ", value);
        this.zctx.setProperty("fontSize", value);
      }
    }
  },

  mounted() {
    const scope = window.Zwibbler.attach(this.$refs.zwibblerContainer, {})
    //this.zctx = scope.ctx
    // uncomment when testing markRaw (and comment above)
    this.zctx = markRaw(scope.ctx)
    this.summary = this.zctx.getPropertySummary();

    // fix images for built-in toolbar
    this.zctx.setConfig("imageFolder", "https://zwibbler.com/");

    // stops font-size from showing when drawing shapes
    this.zctx.setConfig("allowTextInShape", false);

    // Allow images to be cropped
    this.zctx.setConfig("allowCrop", true);

    // Enable snapping of angles to help with drawing on mobile
    this.zctx.setConfig("snapAngle", 15);
    this.zctx.setConfig("snap", 20);
    this.zctx.setConfig("keySnappingOff", "Alt");

    // demonstrate some of the ruler features
    this.zctx.setConfig("background", "grid");
    this.zctx.setConfig("showRuler", true);
    this.zctx.setConfig("pixelsPerUnit", 20);
    this.zctx.setConfig("units", "cm");

    let externalUpdate = () => {
      console.log('EXTERNAL UPDATE')
      this.summary = this.zctx.getPropertySummary()
      this.$forceUpdate();
    }

    this.zctx.on("tool-changed", externalUpdate);
    this.zctx.on("document-changed", externalUpdate);
    this.zctx.on("selected-nodes", externalUpdate);
  },
  beforeUnmount() {
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

zwibbler {
  display: flex;
  flex: 1 1 auto;
}

zwibbler [z-canvas] {
  flex: 1 1 auto;
}
</style>
