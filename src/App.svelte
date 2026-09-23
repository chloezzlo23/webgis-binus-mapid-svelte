<script>
  import MapView from './lib/MapView.svelte';

  let basemap = 'street';
  let mapView;
  let status = 'Basemap only. Points are not loaded yet.';

  function useMvt() {
    mapView.useMvt();
    status = 'MVT is on. Pan the map and watch the small .mvt requests.';
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
    <p class="hint">{status}</p>
    <p class="note">Click a green point to read supplier, plot area, and region.</p>
  </aside>
  <MapView bind:this={mapView} {basemap} />
</div>

<style>
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
