<script>
    import {getContext, onDestroy} from 'svelte';
    import L from 'leaflet';
    
    const {getMap} = getContext(L);
    
    export let position = 'topleft';
    export let options = {};
    
    let zoomControl;
    const optionsWithPosition = {...options, position: position};
    $: {
        if (!zoomControl) {
           zoomControl = L.control.zoom({ position }).addTo(map);
        }
    }
    onDestroy(() => {
        zoomControl.removeFrom(getMap());
    });
    export function getZoomControl() {
        return zoomControl;
    }
</script>
