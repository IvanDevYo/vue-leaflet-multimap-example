<template>
  <div class="map">
      <div class="compare-wrapper bg-white h-64 w-full">
        <div class="compare">
            <l-map
                    class="default-map compare__content"
                    @update:zoom="redraw"
                    :options="{ trackResize: true }"
                    ref="map-1"
                    @move="moveMap('map-2', $event)"
                    @zoomanim="moveMap('map-2', $event)"
                    @zoom="moveMap('map-2', $event)"
                    @mouseover="currentMap = 'map-1'"
                    :style="{'width': width}"
                >
                    <l-tile-layer
                        v-for="tileProvider in tileProviders"
                        :ref="'tile-layer-' + 1"
                        :key="tileProvider.name"
                        :name="tileProvider.name"
                        :visible="tileProvider.visible"
                        :url="tileProvider.url"
                        :attribution="tileProvider.attribution"
                        :tileLayerClass="tileProvider.tileLayerClass"
                        :subdomains="tileProvider.subdomains"
                        layer-type="base"
                    ></l-tile-layer>
                </l-map>
            <!-- <div data-v-b329ee4c="" tabindex="-1" class="resize-observer"><object style="display: block; position: absolute; top: 0; left: 0; height: 100%; width: 100%; overflow: hidden; pointer-events: none; z-index: -1;" aria-hidden="true" tabindex="-1" type="text/html" data="about:blank"></object></div> -->
            <resize-observer @notify="handleResize"></resize-observer>
            <div class="handle-wrap" :style="{left:`calc(${compareWidth + '%'} - var(--handle-line-width) / 2`}"><div class="handle"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="handle__arrow handle__arrow--l"><polyline points="15 18 9 12 15 6"></polyline></svg> <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="handle__arrow handle__arrow--r"><polyline points="9 18 15 12 9 6"></polyline></svg></div> <span class="handle-line"></span></div>
            <div class="compare-overlay " :style="{width:`calc(${compareWidth + '%'})`}">
                <div class="compare-overlay__content" :style="{ 'width': width}">
                    <l-map
                            class="default-map compare-overlay__content"
                            @update:zoom="redraw"
                            :options="{ trackResize: true }"
                            :style="{ 'width': width, zIndex: 2222}"
                            @move="moveMap('map-1', $event)"
                            @zoomanim="moveMap('map-1', $event)"
                            @zoom="moveMap('map-1', $event)"
                            @mouseover="currentMap = 'map-2'"
                            ref="map-2"
                        >
                            <l-tile-layer
                            v-for="tileProvider in tileProviders"
                            :ref="'tile-layer-' + 2"
                            :key="tileProvider.name"
                            :name="tileProvider.name"
                            :visible="tileProvider.visible"
                            :url="tileProvider.url"
                            :attribution="tileProvider.attribution"
                            :tileLayerClass="tileProvider.tileLayerClass"
                            :subdomains="tileProvider.subdomains"
                            layer-type="base"
                        ></l-tile-layer>
                    </l-map>
                </div>
            </div>
        </div>
        <input type="range" min="0" max="100" :step="step" class="compare__range" :value="compareWidth" @input="handleInput" tabindex="-1">
      </div>
  </div>
</template>

<script>
import { LMap, LTileLayer } from 'vue2-leaflet'

const INIT_VALUE = 100

export default {
  name: 'App',
  components: {
    LMap,
    LTileLayer,
  },
  data() {
    return {
        deb: null,
        currentMap: null,
        value: INIT_VALUE,
        step: '.1',
        width: null,
        compareWidth: INIT_VALUE,
        tileProviders: [
          {
            name: 'OpenStreetMap',
            visible: true,
            attribution:
              '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
            url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
          },
        ],
    }
  },
  watch:{
    value() {
      this.compareWidth= this.value
    }
  },
  mounted() {
    this.width = this.getContainerWidth();
    setTimeout(() => {
        this.compareWidth = 50
    })
  },
  methods: {
      moveMap(ref, event) {
          if (this.currentMap !== ref) {
            console.log(ref, event)
            if (event.type !== 'zoomanim') {
                this.$refs[ref].mapObject.fitBounds(event.target.getBounds(), { animate: false })
            } else {
                this.$refs[ref].mapObject.setView(event.center, event.zoom, { duration: 0.01, easeLinearity: 0.01 })
            }
          }
    
      },
      redraw() {
        //   this.$refs['map-1'].$el.trigger('resize')
        //   this.$refs['map-2'].$el.trigger('resize')
        // this.$refs['tile-layer-1'][0].mapObject.redraw()
        //  this.$refs['tile-layer-2'][0].mapObject.redraw()
      },
    handleInput(e){
      this.compareWidth = e.target.value
      this.$emit('input', e.target.value)
      this.handleResize()
    //   this.redraw()
    },
    handleResize(){
      const w = this.getContainerWidth();
      if(w === this.width)
        return;
      this.width = w
      console.log(this.width)
    },
    getContainerWidth(){
      return window.getComputedStyle(this.$el,null).getPropertyValue('width')
    },
  }
}
</script>

<style src="leaflet/dist/leaflet.css"></style>

<style>
.map {
    height: 100vh;
}

.default-map {
    /* position: absolute; */
}
</style>

<style>
:root{
  --handle-bg: blue;
  --handle-width: 30px;
  --handle-height: 30px;
  --handle-chevron-size: 20px;
  
  --handle-line-bg: blue;
  --handle-line-width: 2px;
  --handle-line-height: 100%;
  
  --z-index-handle: 50000;
  --z-index-handle-line: 400000;
  --z-index-range-input: 600000;
}

.compare-wrapper{
  position: relative;
  height: 100%;
}
.compare,
.compare__content{
  position: relative;
  height: 100%;
}

.compare-overlay{
  position: absolute;
  overflow:hidden;
  height: 100%;
  top:0;
}
.compare-overlay__content{
  position:relative;
  height: 100%;
  width: 100%;
}

.handle__arrow{
  position: absolute;
  width: var(--handle-chevron-size);
}
.handle__arrow--l{
  left:0;
}
.handle__arrow--r{
  right:0;
}

.handle-wrap{
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  top: 50%;
  height: 100%;
  transform: translate(-50%, -50%);
  z-index: var(--z-index-handle);
}
.handle{
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  background: var(--handle-bg);
  border-radius: 50%;
  width: var(--handle-width);
  height: var(--handle-height);
}

.handle-line{
  content: '';
  position: absolute;
  top:0;
  width: var(--handle-line-width);
  height: 100%;
  background: var(--handle-line-bg);
  z-index: var(--z-index-handle-line);
  pointer-events:none;
  user-select:none;
}

.compare__range{
  position: absolute;
  cursor: ew-resize;
  left: calc(var(--handle-width) / -2);
  width: calc(100% + var(--handle-width));
  transform: translatey(-50%);
  top: calc(50%);
  z-index: var(--z-index-range-input);
  -webkit-appearance: none;
  height: var(--handle-height);
  /* debugging purposes only */
  background: rgba(0,0,0,.4);
  opacity: .0;
}

.object-fit-cover{
  object-fit: cover;
}
</style>
