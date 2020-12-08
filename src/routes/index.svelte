<script>
  import { onMount } from "svelte";
  import Cam from "../components/Cam/Cam.svelte";
  import Map from "../components/Map/Map.svelte";
//  import Watched from "../../components/Watched/Main.svelte";
//  import Events from "../../components/Events/Main.svelte";
//  import Event from "../../components/Event/Main.svelte";
//  import MapAccount from "../../components/Map/Map.svelte";
//  import { registration } from "../web-push-client.js";

  //  let FData = new FetchData();
  let componentSelected = Cam;
  
  let MenuOpen = false;

  function ToggleClassMenu() {
    console.log("Toogle");
    MenuOpen = !MenuOpen;
  }

  onMount(async () => {
    
  });
</script>

<nav class="navbar" role="navigation" aria-label="main navigation">
  <div class="navbar-brand">
    <a class="navbar-item" href="/home">
      <img src="logo.png" width="25" height="25" alt="Seguridad Comunitaria" />
      <strong> DASHCAM</strong>
    </a>

    <!-- svelte-ignore a11y-missing-attribute -->
    <a
      class:is-active={MenuOpen}
      on:click={ToggleClassMenu}
      role="button"
      class="navbar-burger burger"
      aria-label="menu"
      aria-expanded="false"
      data-target="navbarBasicExample">
      <span aria-hidden="true" />
      <span aria-hidden="true" />
      <span aria-hidden="true" />
    </a>
  </div>

  <div class="navbar-menu" class:is-active={MenuOpen}>
    <div class="navbar-start">
      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="navbar-item"> DASHCAM </a>

      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="navbar-item"> MAPA </a>
    </div>

    <div class="navbar-end">
      <div class="navbar-item">
        <div class="buttons">
          <a class="button is-light" href="/"> Logout </a>
        </div>
      </div>
    </div>
  </div>
</nav>

<div class="tabs is-boxed">
  <ul>
    <!-- svelte-ignore a11y-missing-attribute -->
    <li
      class:is-active={componentSelected === Cam}
      on:click={() => {
        componentSelected = Cam;
      }}>
      <a>
        <span class="icon is-small"><i
            class="fa fa-exclamation-triangle"
            aria-hidden="true" /></span>
        <span>DashCam</span>
      </a>
    </li>
    <!-- svelte-ignore a11y-missing-attribute -->
    <li
      class:is-active={componentSelected === Map}
      on:click={() => {
        componentSelected = Map;
      }}>
      <a>
        <span class="icon is-small"><i
            class="fa fa-list-alt"
            aria-hidden="true" /></span>
        <span>Mapa</span>
      </a>
    </li>
  </ul>
</div>
<svelte:component
  this={componentSelected}
  
  on:event_selected={(e) => {
    componentSelected = Event;
  }} />