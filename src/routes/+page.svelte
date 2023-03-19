<script>
	import MovieItem from '../Movie/Item.svelte';
	import { fly } from 'svelte/transition';

    //información de la API de películas
	const APIKEY = 'ae400a40d88a43d70784439ddd56ff81';
	const BASEURL = 'https://api.themoviedb.org/3';
	const APISETTINGS = `?api_key=${APIKEY}&languaje=es-MX`;

    //función asíncrona para cargar las películas
	const movies = (async () => {
		const URL = `${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`;
		const response = await fetch(URL);

		return await response.json();
	})();

    //función y arreglo para seleccionar las películas favoritas
	let likedMovies = [];

	function toggleLike(event) {
		console.log(event);
		const movie = event.detail;

		let index = likedMovies.findIndex((m) => m.id === movie.id);
		if (index >= 0) {
			likedMovies.splice(index, 1);
		} else {
			likedMovies.push(movie);
		}

		//el renderizado de svelte se hace cada igualacion, así que para renderizarlo debemos hacer una asignación
		likedMovies = likedMovies;
		console.log(likedMovies);
	}

	//computed function en svelte
    //esta función sirve para activar/desactivar una película de favoritos
	$: isLike = (id) => {
		let index = likedMovies.findIndex((m) => m.id === id);
		return index >= 0;
	};
</script>

<svelte:head>
	<title>Movies Svelte</title>
	<link
		href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css"
		rel="stylesheet"
		integrity="sha384-GLhlTQ8iRABdZLl6O3oVMWSktQOp6b7In1Zl3/Jr59b6EGGoI1aFkw7cmDA6j6gD"
		crossorigin="anonymous"
	/>
</svelte:head>

<main class="container">
	<div class="row">
		<div class="col-12 col-md-6 col-lg-7 panel general">
			<h2>Peliculas populares</h2>
			<div class="row">
				{#await movies}
					<!-- promise is pending -->
					<div class="col-12">
						<p>Cargando datos...</p>
					</div>
				{:then data}
					{@debug data}
					<!-- promise was fulfilled -->
					{#each data.results as movie}
						<div class="col-12 col-md-6 col-lg-4 p-2">
							<MovieItem
								isLike={isLike(movie.id)}
								id={movie.id}
								title={movie.title}
								overView={movie.overview}
								cover={movie.poster_path}
								on:onToggleLike={toggleLike}
								size={true}
							/>
						</div>
					{/each}
				{/await}
			</div>
		</div>

		<div class="col-12 col-md-6 col-lg-5 panel fav">
			<h2>Películas favoritas</h2>
			<div class="row">
				{#if likedMovies.length > 0}
					{#each likedMovies as movie, i (movie.id)}
						<div
							in:fly={{ duration: 200, y: -20 }}
							out:fly={{ duration: 200, y: -20 }}
							class="col-12 col-md-6 col-lg-4 p-2"
						>
							<MovieItem
								isLike={isLike(movie.id)}
								id={movie.id}
								title={movie.title}
								overView=""
								cover={movie.cover}
								on:onToggleLike={toggleLike}
								size={false}
							/>
						</div>
					{/each}
				{:else}
					<div class="col-12">
						<p>No tienes películas favoritas</p>
					</div>
				{/if}
			</div>
		</div>
	</div>
</main>

<style>
	.panel {
		height: 200vh;
		overflow: auto;
	}

	.general {
		background-color: gray;
	}

	.fav {
		background-color: lightgray;
	}
</style>
