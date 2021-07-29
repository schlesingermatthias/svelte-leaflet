<script>
    import {getContext, onDestroy} from 'svelte';
    import L from 'leaflet';
    
    const {getMap} = getContext(L);
    
    export let position = 'topleft';
    export let options = {};
    
    let zoomControl;
    const optionsWithPosition = {...options, position: position};
    $: {
        if (!zoomControl && L.control.zoom) {
            zoomControl = L.control.zoom(optionsWithPosition).addTo(getMap());
        } else if(!zoomControl) {
            zoomControl = L.control.zoom(optionsWithPosition);
        }
        zoomControl.setPosition(position);
    }
    onDestroy(() => {
        zoomControl.removeFrom(getMap());
    });
    export function getZoomControl() {
        return zoomControl;
    }
</script>
