<script>
  import { onMount } from "svelte";
  import Menu from "../../components/Menu.svelte";
  import StockTienda from "../../components/RegularizarInventario.svelte";
  import { FetchData } from "../../components/FetchData.js";
  import { UrlSearch } from "../../components/UrlSearch.js";

  let FData = new FetchData();
  export let segment;
  let promise = new Promise(() => {});
  let compania = "X";
  let Material = "";

function HandleSearch(){
promise =   ObtenerOficinas();
}

  async function ObtenerOficinas() {
    compania = UrlSearch("compania");

    let query = { compania: compania };
    const res = await FData.get(
      "/WSMovimientosInventarios/WSMovInv/ConsultarOficinasJSON",
      query,
      {
        "Content-Type": "application/json",
      }
    );

    console.log(res);

    if (res.ok) {
      return res.json();
    } else {
      throw new Error("No se pudo cargar la información");
    }
  }

  onMount(async () => {
    compania = UrlSearch("compania");
  });
</script>

<Menu {segment}>
  <span slot="Title">
    <i class="fas fa-shield-alt" /> OPEN MONITORING SYSTEM
  </span>
</Menu>

<!-- Main container -->
<nav class="level">
  <!-- Left side -->
  <div class="level-left">
    <div class="level-item">
      <p class=""><strong>INVENTARIOS {compania}</strong></p>
    </div>
    
  </div>

  <!-- Right side -->
  <div class="level-right">
    <div class="level-item">
      <div class="field has-addons">
        <p class="control">
          <input class="input is-small" type="text" bind:value="{Material}" placeholder="Consultar material" />
        </p>
        <p class="control"><button class="button  is-small" on:click="{HandleSearch}"> Consultar Material </button></p>
      </div>
    </div>
  </div>
</nav>

<div class="columns is-multiline is-mobile root container">
  {#await promise}
    <p>...esperando</p>
  {:then datas}
    {#each datas as data}
      <div class="column is-one-quarter">
        <StockTienda Compania={compania} Tienda={data.Nombre} TiendaSAP={data.ofi_codigo_interno_empresa} Material={Material}/>
      </div>
    {/each}
  {:catch error}
    <p style="color: red">{error.message}</p>
  {/await}
</div>
