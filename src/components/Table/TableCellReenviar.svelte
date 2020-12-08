<script>
  export let value;
  export let row = {};
  let reenviado = false;
  
  async function Reenviar() {
    console.log(value);

try {
  let response = await fetch(value, {
      mode: 'no-cors',
      //referrerPolicy: "no-referrer", // no-referrer, origin, same-origin...
      headers:{
    'Content-Type': 'application/json'
  }
  
    });

    console.log(response);
      reenviado = true;

    //let data = await response.text();
    //console.log(data);

} catch (error) {
  console.error(error);
  reenviado = false;
}


  }
  
</script>


<style>
</style>

{#if row.tipo_documento == 'E' || row.tipo_documento == 'O'}
<td >
  
</td>
{:else}
<td class="has-background-light has-text-centered">
  <!-- <a  target="_blank" download="Reeenviar" class="button is-link is-small" href={value}>Reenviar</a> -->
  <span class:is-link="{reenviado}" class="button is-dark is-small"  on:click={Reenviar}>Reenviar</span>
</td>  
{/if}


