<script>
  import { onMount } from "svelte";

  let showMobile = false;

  const navItems = [];
</script>

<main>
  <nav class="navbar">
    <ul>
      {#each navItems as item}
        <li>
          <a href={item.href}>{item.label} </a>
        </li>
      {/each}
    </ul>
  </nav>
</main>

<style>
  a {
    color: white;
    font-size: 1.5em;
  }
  li a:hover {
    color: gray;
  }
  ul {
    display: flex;
    justify-content: right;
  }

  li {
    list-style-type: none;
    padding-left: 1em;
    padding-right: 1em;
  }
  ul {
    margin: 0;
  }
  .navbar {
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1;
    background-color: #0988d7;
    height: 30px;
    overflow: hidden;
  }
  main {
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1;
    height: 30px;
    overflow: hidden;
    width: 100%;
  }
  @media (max-width: 420px) {
    a {
      font-size: 0.85em;
    }
    li {
      padding: 8px;
    }
  }
</style>
