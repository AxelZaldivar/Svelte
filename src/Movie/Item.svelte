<script>
    import { createEventDispatcher } from 'svelte';

    export let id;
    export let title;
    export let overView;
    export let cover;
    export let isLike;
    export let size;

    $: coverURL = `https://image.tmdb.org/t/p/w185_and_h278_bestv2${cover}`;

    const dispatch = createEventDispatcher();
    function toggleLike(){
        dispatch('onToggleLike',{id, title, overView, cover})
    }
</script>

<div class={size ? 'card big' : 'card small'}>
    <img src={coverURL} class="card-img-top" alt="...">
    <div class="card-body">
      <h5 class="card-title">{title}</h5>
      <p class="card-text">{overView}</p>
      <div class="center">
        <button class={isLike ? 'btn btn-secondary' : 'btn btn-primary'} on:click={toggleLike}>{isLike ? 'Ya no me gusta' : 'Me gusta'}</button>
      </div>
    </div>
  </div>

  <style>
    .big{
        height: 100vh;
        overflow: auto;
    }

    .small{
        height: 65vh;
        overflow: auto;
    }
  </style>