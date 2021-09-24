<template>
    <div class="four-map">
        <div class="map-container">
            <l-map
                class="four-map-leaflet"
                @move="debouncedMoveMap('map-1', $event)"
                @zoomanim="moveMap('map-1', $event)"
                @zoom="moveMap('map-1', $event)"
                @mousemove="mouseMove('map-1', $event)"
                @mouseover="mouseOver('map-1')"
                @mouseout="mouseOut"
                ref="map-1"
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
        <div class="map-container">
            <l-map
                class="four-map-leaflet"
                @move="debouncedMoveMap('map-2', $event)"
                @zoomanim="moveMap('map-2', $event)"
                @zoom="moveMap('map-2', $event)"
                @mousemove="mouseMove('map-2', $event)"
                @mouseover="mouseOver('map-2')"
                @mouseout="mouseOut"
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
        <div class="map-container">
            <l-map
                class="four-map-leaflet"
                @move="debouncedMoveMap('map-3', $event)"
                @zoomanim="moveMap('map-3', $event)"
                @zoom="moveMap('map-3', $event)"
                @mousemove="mouseMove('map-3', $event)"
                @mouseover="mouseOver('map-3')"
                @mouseout="mouseOut"
                ref="map-3"
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
        <div class="map-container">
            <l-map
                class="four-map-leaflet"
                @move="debouncedMoveMap('map-4', $event)"
                @zoomanim="moveMap('map-4', $event)"
                @zoom="moveMap('map-4', $event)"
                @mousemove="mouseMove('map-4', $event)"
                @mouseover="mouseOver('map-4')"
                @mouseout="mouseOut"
                ref="map-4"
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
</template>

<script>
import { LMap, LTileLayer } from 'vue2-leaflet'
import L from 'leaflet'
import debounce from 'lodash/debounce'

export default {
    name: 'FourMap',
    components: {
        LMap,
        LTileLayer,
    },
    mounted() {
        this.deb = debounce(this.mouseMove, 500)
        this.debouncedMoveMap = debounce(this.moveMap, 1)
    },
    data() {
        return {
            tileProviders: [
                {
                    name: 'OpenStreetMap',
                    visible: true,
                    attribution:
                    '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
                    url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                },
            ],
            mapRefs: ['map-1', 'map-2', 'map-3', 'map-4'],
            mapPopups: {},
            currentMapObjectRef: null,
            cursorLatLng: [0, 0],
        }
    },
    methods: {
        initPopups() {
            for (let i = 0; i < this.mapRefs.length; i++) {
                setTimeout(() => {
                    const neededRef = this.mapRefs[i]
                    this.mapPopups[neededRef + '-popup'] = L.popup({
                        closeButton: false,
                    })
                        .setLatLng(this.cursorLatLng)
                        .setContent('1')
                        .openOn(this.$refs[neededRef].mapObject)
                })
            }
        },
        mouseOver(ref) {
            console.log('mouseover')
            this.currentMapObjectRef = ref
            this.initPopups()
        },
        mouseOut() {
            console.log('mouseout')
            this.currentMapObjectRef = null
            for(let i in this.mapPopups) {
                if (this.mapPopups[i]) {
                    this.mapPopups[i].remove()
                    this.mapPopups[i] = null
                }
            }
        },
        moveMap(ref, event) {
            console.log(event.type)
            if (this.currentMap !== ref) {
                const neededRefs = this.mapRefs.filter(item => item !== ref)
                for (let i = 0; i < neededRefs.length; i++) {
                    const neededRef = neededRefs[i]
                    if (event.type !== 'zoomanim') {
                        this.$refs[neededRef].mapObject.fitBounds(event.target.getBounds(), { animate: false })
                    } else {
                        this.$refs[neededRef].mapObject.setView(event.center, event.zoom, { duration: 0.01, easeLinearity: 0.01 })
                    }
                }
                
            }
        },
        mouseMove(ref, event) {
            console.log(event.type)
            this.cursorLatLng = event.latlng
            if (this.currentMapObjectRef) {
                for (let i in this.mapPopups) {
                    if (this.mapPopups[i]) {
                        this.mapPopups[i]
                            .setLatLng(this.cursorLatLng)
                            .setContent('latLng: ' + this.cursorLatLng.lat + ', ' + this.cursorLatLng.lng)
                    }
                }
            }
            
        },
    },
}
</script>

<style>
.four-map-leaflet {
    height: 100vh;
    width: 100%;
}

.four-map {
    height: 100vh;
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
}

.map-container {
    width: calc(50% - 5px);
    height: 50%;
    margin-bottom: 10px;
}

</style>