<script>
  import { onMount } from "svelte";
  import { FetchData } from "../components/FetchData.js";

  export let Material = "";
  export let Tienda = "Tienda";
  export let TiendaSAP = "";
  export let Compania = "";
  let SerieRegularizacion = "";
  let ConsultandoInvPV = false;
  let ConsultandoInvSAP = false;

  let FData = new FetchData();
  let DataInventario = {};

  async function Regularizar() {
    let CompaniaSAP = "";
    switch (Compania) {
      case "ETA":
        CompaniaSAP = "E001";
        break;
      case "RM":
        CompaniaSAP = "E002";
        break;
      case "CR":
        CompaniaSAP = "E004";
        break;
    }

    const res = await FData.get(
      "/WSMovimientosInventarios/WSMovInv/Regularizar",
      {
        compania: Compania,
        CompaniaSAP: CompaniaSAP,
        OficinaSAP: TiendaSAP,
        Material: Material,
        CantidadSAP: DataInventario.StockSAP,
        CantidadPV: DataInventario.StockPV,
        UserName: "webapp",
      },
      {
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (res.ok) {
      SerieRegularizacion = await res.json();
      console.log(SerieRegularizacion);
    } else {
      console.error(res);
    }
  }

  async function ConsultaStockTienda() {
    DataInventario.StockPV = undefined;
    DataInventario.Material = undefined;
    DataInventario.Descripcion = undefined;
    ConsultandoInvPV = true;
    const res = await FData.get(
      "/WSMovimientosInventarios/WSMovInv/ConsultaUltimoMovimientoJSON",
      { CodOficinaSAP: TiendaSAP, Material: Material.trim(), compania: Compania },
      {
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (res.ok) {
      let d = await res.json();
      if (Array.isArray(d)) {
        if (d.length > 0) {
          DataInventario.StockPV = d[0].Cantidad_Total;
          DataInventario.Material = d[0].codigo_producto;
          DataInventario.Descripcion = d[0].descripcion;
        }
      }

      console.log(DataInventario);
    } else {
      console.error(res);
    }
    ConsultandoInvPV = false;
  }

  async function ConsultaStockSAP() {
    DataInventario.StockSAP = undefined;
    ConsultandoInvSAP = true;
    const res = await FData.get(
      "/WSMovimientosInventarios/WSMovInv/ConsultaStockSAP",
      { tienda: TiendaSAP, material: Material.trim().padStart(18, "0"), compania: Compania },
      {
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (res.ok) {
      let d = await res.json();
      //console.log(d, d.Inventario);
      
      if (Array.isArray(d.Inventario)) {
        if (d.Inventario.length > 0) {
          DataInventario.StockSAP = d.Inventario[0].Stockactual;
        }
      }

      console.log(DataInventario);
      
    } else {
      console.error(res);
    }
    ConsultandoInvSAP = false;
  }

  onMount(async () => {
    console.log(TiendaSAP);
    ObtenerStocks();
  });

function VerificarStock(){
let r = false;

if(DataInventario.StockPV !== undefined && DataInventario.StockSAP !== undefined  && DataInventario.StockPV != DataInventario.StockSAP){
r = true;
}

console.log(DataInventario, r);
  return r;     
}

  function ObtenerStocks(){
    ConsultaStockTienda();
    ConsultaStockSAP();
  }

</script>

<style>

</style>

<div class="card">
  <header class="card-header">
    <p class="card-header-title">{Tienda}</p>
  </header>
  <div class="card-content">
    <div class="content">
      <div class="">
        <span>
          <div class="field">
            <label class="label is-small">Material</label>
            <div class="control is-small">
              <input
              class:is-success={DataInventario.StockPV !== undefined && DataInventario.StockSAP !== undefined  && DataInventario.StockPV == DataInventario.StockSAP}              
              class:is-danger={DataInventario.StockPV !== undefined && DataInventario.StockSAP !== undefined  && DataInventario.StockPV != DataInventario.StockSAP}
              class:is-warning={DataInventario.StockPV == undefined || DataInventario.StockSAP == undefined}
                type="text"
                class="input is-small"
                value={DataInventario.Material}
                readonly />
            </div>
          </div>
        </span>
      </div>
      <div class="">
        <span>
          <div class="field">
            <label class="label is-small">Descripción</label>
            <div class="control is-small">
              <input
              class:is-success={DataInventario.StockPV !== undefined && DataInventario.StockSAP !== undefined  && DataInventario.StockPV == DataInventario.StockSAP}              
              class:is-danger={DataInventario.StockPV !== undefined && DataInventario.StockSAP !== undefined  && DataInventario.StockPV != DataInventario.StockSAP}
              class:is-warning={DataInventario.StockPV == undefined || DataInventario.StockSAP == undefined}
                type="text"
                class="input is-small"
                value={DataInventario.Descripcion}
                readonly />
            </div>
          </div>
        </span>
      </div>      
      <div class="">
        <span>
          <div class="field">
            <label class="label is-small">Stock Tienda</label>
            <div class="control is-small" class:is-loading="{ConsultandoInvPV}">
              <input
                placeholder="Stock PV"
                class:is-warning={DataInventario.StockPV === undefined}
                type="number"
                class="input is-small"
                value={DataInventario.StockPV}
                readonly />
            </div>
          </div>
        </span>
      </div>
      <div class="">
        <span>
          <div class="field">
            <label class="label is-small">Stock SAP</label>
            <div class="control is-small" class:is-loading="{ConsultandoInvSAP}">
              <input
              class:is-warning={DataInventario.StockSAP === undefined}
                
                placeholder="Stock SAP"
                type="number"
                class="input is-small"
                bind:value={DataInventario.StockSAP} />
            </div>
          </div>
        </span>
      </div>
      <div class="">
        <span>
          <div class="field">
            <label class="label is-small">Serie Doc. Ajuste</label>
            <div class="control is-small">
              <input
                readonly
                placeholder="Ajuste"
                type="text"
                class="input is-small"
                bind:value={SerieRegularizacion} />
            </div>
          </div>
        </span>
      </div>
    </div>
  </div>
  <footer class="card-footer">
    <!-- svelte-ignore a11y-invalid-attribute -->
    <!-- svelte-ignore a11y-missing-attribute -->
    <a class="card-footer-item" on:click={ObtenerStocks}>Consultar</a>
    <!-- svelte-ignore a11y-invalid-attribute -->
    <!-- svelte-ignore a11y-missing-attribute -->
    {#if DataInventario.StockPV !== undefined && DataInventario.StockSAP !== undefined  && DataInventario.StockPV == DataInventario.StockSAP}
      <label class="card-footer-item">Correcto</label>
      {:else if SerieRegularizacion.length > 0}  
      <label class="card-footer-item">Enviado</label>
    {:else}
      <a class="card-footer-item" on:click={Regularizar}>Regularizar {SerieRegularizacion}</a>
    {/if}
  </footer>
</div>
