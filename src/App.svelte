<script lang="ts">

	import neo4j from 'neo4j-driver';

	const neo4jConfig = {
		username: process.env.NEO4J_USERNAME,
		password: process.env.NEO4J_PASSWORD
	}

	const driver = neo4j.driver("neo4j://localhost:7687",
		neo4j.auth.basic(neo4jConfig.username, neo4jConfig.password)
	);

	let cypher = "MATCH (n) RETURN n LIMIT 10";

	let promisedResult;

	function send() {
		const session = driver.session();
		promisedResult = session.run(cypher); 
	}

</script>

<main>

	<h1>Basic Svelte Neo4j</h1>
	<p>See <a href="http://github.com/akollegger/basic-svelte-neo4j">akollegger/basic-svelte-neo4j</a> for details.</p>
	
	<input
		size=80
		placeholder="What needs to be done?"
		bind:value={cypher}
	>
	<button on:click={send}>
		Send
	</button>

	<div>
		<h3>Results</h3>
		{#await promisedResult}
			<p>...waiting</p>
		{:then results}
			{#if results}
				{#each results.records as record}
					<pre>{JSON.stringify(record)}</pre>
				{/each}
			{/if}
		{:catch error}
			<p style="color: red">{error.message}</p>
		{/await}
	</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>