<script>
  import MapView from './lib/MapView.svelte';

  let basemap = 'street';
  let mapView;
  let status = 'Basemap only. Points are not loaded yet.';
  let layerOn = false;

  function useMvt() {
    mapView.useMvt();
    status = 'MVT is on. Pan the map and watch the small .mvt requests.';
  }

function toggleLayer() {
  layerOn = !layerOn;
  mapView.toggleSupplierLayer(layerOn);

  status = layerOn
    ? 'Supplier points are ON.'
    : 'Supplier points are OFF.';
}

  const options = [
    { id: 'street', label: 'MAPID Street 2D' },
    { id: 'light', label: 'MAPID Light' },
    { id: 'satellite', label: 'MAPID Satellite' },
    { id: 'demo', label: 'MapLibre Demo (fallback)' }
  ];
</script>

<div class="app">
  <aside>
    <p class="kicker">MAPID × BINUS</p>
    <h1>Supplier WebGIS</h1>
    <label for="basemap">Basemap</label>
    <select id="basemap" bind:value={basemap}>
      {#each options as option}
        <option value={option.id}>{option.label}</option>
      {/each}
    </select>
    <button type="button" on:click={useMvt}>Use MVT</button>

<div class="layer-section">
  <p class="section-title">Layers</p>

  <button
    type="button"
    class:active={layerOn}
    on:click={toggleLayer}
  >
    <span class="layer-dot"></span>
    <span class="layer-name">Supplier Points</span>
    <span class="layer-status">{layerOn ? 'ON' : 'OFF'}</span>
  </button>
</div>

    <p class="hint">{status}</p>
    <p class="note">Click a green point to read supplier, plot area, and region.</p>
  </aside>
  <MapView bind:this={mapView} {basemap} />
</div>

<style>

.layer-section {
  margin-top: 18px;
}

.section-title {
  margin: 0 0 8px;
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  color: #6b6458;
}

.layer-section button {
  display: flex;
  align-items: center;
  gap: 9px;
  margin-top: 0;
  text-align: left;
}

.layer-dot {
  width: 10px;
  height: 10px;
  flex-shrink: 0;
  border-radius: 50%;
  background: #b8b1a5;
}

.layer-section button.active .layer-dot {
  background: #ec4899;
}

.layer-name {
  flex: 1;
}

.layer-status {
  font-size: 11px;
  font-weight: 700;
  color: #8a8275;
}

.layer-section button.active .layer-status {
  color: #ec4899;
}

  .app {
    display: flex;
    height: 100%;
    min-height: 100vh;
  }

  aside {
    width: 280px;
    flex-shrink: 0;
    padding: 20px 18px;
    background: #f4f1ea;
    border-right: 1px solid #ddd6c8;
    box-sizing: border-box;
  }

  .kicker {
    margin: 0 0 8px;
    font-size: 12px;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    color: #6b6458;
  }

  h1 {
    margin: 0 0 20px;
    font-size: 22px;
    line-height: 1.2;
    font-weight: 650;
  }

  label {
    display: block;
    margin-bottom: 6px;
    font-size: 13px;
    font-weight: 600;
  }

  select,
  button {
    width: 100%;
    padding: 8px 10px;
    font: inherit;
    background: #fff;
    border: 1px solid #c9c1b2;
    border-radius: 6px;
  }

  button {
    margin-top: 8px;
    text-align: left;
    cursor: pointer;
  }

  .hint,
  .note {
    margin: 16px 0 0;
    font-size: 14px;
    line-height: 1.45;
    color: #3f3a33;
  }

  .note {
    margin-top: 8px;
    color: #6b6458;
  }
</style>
