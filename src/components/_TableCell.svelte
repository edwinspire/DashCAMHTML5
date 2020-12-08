<script>
  import moment from "moment";
  import TableCellPriority from "./_TableCellPriority.svelte";
  import TableCellReenviar from "./TableCellReenviar.svelte";
  import { onMount } from "svelte";
  

  export let type;
  export let value;
  export let row = {};

  function ClickCell(d) {
    console.log(row);
  }

  
  onMount(async () => {

    console.log(type, value);

  });
</script>

<style>

</style>

{#if type}
console.log('Tipo de celda', type);
  {#if type === 'Date'}
    <td on:click={ClickCell}>
      {moment(Date.parse(value)).format('YYYY-MM-DD HH:mm')}
    </td>
  {:else if type === 'Priority'}
    <TableCellPriority {value} {row} />
  {:else if type === 'BtnReenviar'}
    <TableCellReenviar {value} {row} />    
  {:else if type === 'Boolean-Color'}
    {#if !value}
      <td
        on:click={ClickCell}
        class="has-background-danger has-text-white has-text-centered">
        <i class="fa fa-times" aria-hidden="true" />
      </td>
    {:else}
      <td
        on:click={ClickCell}
        class="has-background-success has-text-white has-text-centered">
        <i class="fa fa-check" aria-hidden="true" />
      </td>
    {/if}
  {:else}
    <td on:click={ClickCell}>{value}</td>
  {/if}
{:else}
  <td on:click={ClickCell}>{value}</td>
{/if}
