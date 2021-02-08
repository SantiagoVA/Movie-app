<script>
	import MovieItem from './Movie/item.svelte';
	import { fly } from 'svelte/transition';
	const APIKEY = '6ecc816338267e2ec9b5812bab07e810';
	const BASEURL = `https://api.themoviedb.org/3`;
	const APISETTINGS = `?api_key=${APIKEY}&language=es-MX`;


	
	const movies = (async () => {
		const URL = `${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`;
		const response = await fetch(URL);
		
		return await response.json();
	})();

	let likedMovies = [];

	function onToggleLike(event){
		const movie = event.detail;
		let index = likedMovies.findIndex(m => m.id === movie.id);

		if(index >= 0){
			likedMovies.splice(index, 1)
			console.log(likedMovies)
			likedMovies = likedMovies;
				return;
			}
			likedMovies.push(movie);
			console.log(likedMovies)
			likedMovies = likedMovies;
		}
	
		$: like = (id) => {
			let index = likedMovies.findIndex(m => m.id === id);
			return index >= 0;
		}
</script>

<svelte:head>
	<title>Movies with svelte</title>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css" integrity="sha384-B0vP5xmATw1+K9KRQjQERJvTumQW0nPEzvF6L/Z6nronJ3oUOFUFpCjEUQouq2+l" crossorigin="anonymous">
</svelte:head>

<section class="main-section">
	<h1 class="title">Movie API</h1>
</section>

<main class="container">
	<div class="row">
		<div class="col-12 col-md-6 col-lg-8 border panel">
			<h2>Peliculas populares</h2>
			<div class="row">
				{#await movies}
				<div class="col-12"><p>Cargando datos...</p></div>
				{:then data}
				{@debug data}
				{#each data.results as movie}
				<div in:fly="{{duration: 1000, y: 30}}"  class="col-12 col-md-6 col-lg-4">
						<MovieItem like ={like(movie.id)} id={movie.id} title={movie.title} overview={movie.overview} cover={movie.poster_path} on:onToggleLike={onToggleLike} />	
				</div>
				{/each}
				{/await}
			</div>
		</div>
		<div class="col-12 col-md-6 col-lg-4 border panel">
			<h2>Peliculas favoritas</h2>
			<div class="row">
				{#if likedMovies.length}
			{#each likedMovies as movie, i(movie.id)}
				<div in:fly="{{duration: 500, y: 20}}"  out:fly="{{duration: 800, y: -20}}" class="col-12 col-md-6 col-lg-6">
					<MovieItem like={like(movie.id)} id={movie.id} title={movie.title} overview="" cover={movie.cover} on:onToggleLike={onToggleLike} />	
				</div>			
			{/each}								
				{:else}
				<div class="col-12">
					<p>No has añadido peliculas favoritas</p>
				</div>
				{/if}
				
				
			</div>
		</div>
	</div>
	
</main>

<style>
	.title {
		position: relative;
		top: 30%;
		text-align: center;
		color: white;
	}
	
	.main-section {
		width: 100%;
		height: 100vh;
		background-image: url('https://image.freepik.com/vector-gratis/telon-rojo-teatro-cortina-escena-opera_107791-1552.jpg');
		background-size: cover;
	}
	main {
		padding-top: 10%;
		text-align: center;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	.panel {
		height: 100vh;
		overflow: auto;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>