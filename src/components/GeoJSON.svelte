<script>
    import {createEventDispatcher, getContext, onDestroy, setContext} from 'svelte';
    import L from 'leaflet';
    import axios from 'axios';

    import EventBridge from '../lib/EventBridge';

    const {getMap} = getContext(L);

    export let url;
    export let options = {};
    export let events = [];
    export let geojson = null;
    let geojsonLayer;

    setContext(L.Layer, {
        getLayer: () => geojsonLayer,
    });

    const dispatch = createEventDispatcher();
    let eventBridge;
    let previousHash = null;

    $: {
        const hash = JSON.stringify(geojson);
        if (geojson && geojsonLayer && hash !== previousHash) {
            geojsonLayer.clearLayers();
            geojsonLayer.addData(geojson);
            previousHash = hash;
        }
        if (!geojsonLayer) {
            geojsonLayer = L.geoJSON(geojson, options).addTo(getMap());
            eventBridge = new EventBridge(geojsonLayer, dispatch, events);
        }
        if(!geojson) {
            axios.get(url)
                .then(result => {
                    geojsonLayer.clearLayers();
                    geojsonLayer.addData(result.data);
                });
        }
    }

    onDestroy(() => {
        eventBridge.unregister();
        geojsonLayer.removeFrom(getMap());
    });

    export function getGeoJSON() {
        return geojsonLayer;
    }
</script>

<div>
    {#if geojsonLayer}
        <slot/>
    {/if}
</div>
