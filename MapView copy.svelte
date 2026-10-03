<script>
  import { onMount, onDestroy } from 'svelte';
  import maplibregl from 'maplibre-gl';
  import {
    basemaps,
    MVT_TILES,
    MVT_SOURCE_LAYER,
    MAP_CENTER,
    MAP_ZOOM
  } from './config.js';

  export let basemap = 'street';

  let container;
  let map;
  let appliedBasemap = basemap;
  let popup;
  let layerMode = 'none';

  const SOURCE_ID = 'suppliers';
  const LAYER_ID = 'suppliers-circles';

  const circlePaint = {
    'circle-radius': ['interpolate', ['linear'], ['zoom'], 6, 3, 12, 6, 16, 9],
    'circle-color': '#ec4899',
    'circle-stroke-width': 1,
    'circle-stroke-color': '#ffffff'
  };

  function escapeHtml(value) {
    return String(value ?? '—')
      .replaceAll('&', '&amp;')
      .replaceAll('<', '&lt;')
      .replaceAll('>', '&gt;');
  }

  function onSupplierClick(event) {
  const props = event.features?.[0]?.properties;
  if (!props) return;

  const area = props.plotareaha ?? '—';

  const popupContent = `
    <div class="popup-card">
      <div class="popup-header">
        <div class="popup-title">SUPPLIER</div>
        <div class="popup-name">${escapeHtml(props.entityname)}</div>
      </div>

      <div class="popup-body">
        <div class="popup-item">
          <span class="popup-label">FID</span>
          <span class="popup-value">${escapeHtml(props.FID)}</span>
        </div>

        <div class="popup-item">
          <span class="popup-label">Plot Area</span>
          <span class="popup-value">${escapeHtml(area)} ha</span>
        </div>

        <div class="popup-item">
          <span class="popup-label">Region</span>
          <span class="popup-value">${escapeHtml(props.regionlabel)}</span>
        </div>
      </div>
    </div>
  `;

  popup
    .setLngLat(event.lngLat)
    .setHTML(popupContent)
    .addTo(map);
}

  function onPointer() {
    map.getCanvas().style.cursor = 'pointer';
  }

  function onPointerOut() {
    map.getCanvas().style.cursor = '';
  }

  function clearSupplierLayer() {
    if (!map.getStyle()) return;
    if (map.getLayer(LAYER_ID)) {
      map.off('click', LAYER_ID, onSupplierClick);
      map.off('mouseenter', LAYER_ID, onPointer);
      map.off('mouseleave', LAYER_ID, onPointerOut);
      map.removeLayer(LAYER_ID);
    }
    if (map.getSource(SOURCE_ID)) map.removeSource(SOURCE_ID);
  }

  function bindClicks() {
    map.on('click', LAYER_ID, onSupplierClick);
    map.on('mouseenter', LAYER_ID, onPointer);
    map.on('mouseleave', LAYER_ID, onPointerOut);
  }

  // setStyle drops custom layers. style.load calls this again for the active mode.
  function applyLayer() {
    if (!map.getStyle()) return;
    clearSupplierLayer();
    if (layerMode !== 'mvt') return;
    map.addSource(SOURCE_ID, {
      type: 'vector',
      tiles: [MVT_TILES],
      minzoom: 0,
      maxzoom: 14
    });
    map.addLayer({
      id: LAYER_ID,
      type: 'circle',
      source: SOURCE_ID,
      'source-layer': MVT_SOURCE_LAYER,
      paint: circlePaint
    });
    bindClicks();
  }

  export function useMvt() {
    layerMode = 'mvt';
    applyLayer();
  }

export function toggleSupplierLayer(isOn) {
  layerMode = isOn ? 'mvt' : 'none';
  applyLayer();

  if (!isOn) {
    popup?.remove();
  }
}

  onMount(() => {
    popup = new maplibregl.Popup({ closeButton: true, closeOnClick: true });
    map = new maplibregl.Map({
      container,
      style: basemaps[basemap],
      center: MAP_CENTER,
      zoom: MAP_ZOOM
    });
    map.addControl(new maplibregl.NavigationControl(), 'top-right');
    map.on('style.load', applyLayer);
  });

  // diff:false makes style.load fire again. The default diff drops custom layers quietly.
  $: if (map && basemap !== appliedBasemap) {
    appliedBasemap = basemap;
    popup?.remove();
    map.setStyle(basemaps[basemap], { diff: false });
  }

  onDestroy(() => {
    popup?.remove();
    map?.remove();
  });
</script>

<div class="map" bind:this={container}></div>

<style>
  .map {
    flex: 1;
    height: 100%;
    min-height: 400px;
  }

  .popup-card {
    min-width: 220px;
    font-family: Arial, sans-serif;
  }

  .popup-header {
    padding-bottom: 12px;
    border-bottom: 1px solid #e5e7eb;
    margin-bottom: 12px;
  }

  .popup-title {
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 1px;
    color: #0f6f4a;
    margin-bottom: 4px;
  }

  .popup-name {
    font-size: 18px;
    font-weight: 700;
    color: #1f2937;
  }

  .popup-body {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .popup-item {
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .popup-label {
    font-size: 11px;
    color: #6b7280;
  }

  .popup-value {
    font-size: 13px;
    font-weight: 600;
    color: #1f2937;
  }
</style>
