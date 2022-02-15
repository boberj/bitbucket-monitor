<script>
	import { onMount } from "svelte";

	let server = "https://bitbucket.navico.com";
	let username = "";
	let token = "";
	let work = [];

	onMount(async () => {
		const settings = await browser.storage.local.get({
			connection: {
				server,
				username,
				token,
			},
		});

		server = settings.connection.server;
		username = settings.connection.username;
		token = settings.connection.token;
	});

	async function load() {
		const settings = {
			connection: {
				server,
				username,
				token,
			},
		};
		await browser.storage.local.set(settings);

		const baseUrl = `${server}/rest/api/1.0`;
		const dashboard = "/dashboard/pull-requests";
		const url = baseUrl + dashboard;
		const init = {
			headers: new Headers({
				Authorization: "Basic " + btoa(`${username}:${token}`),
			}),
			credentials: "include",
		};

		fetch(url, init)
			.then(response => {
				if (!response.ok) {
					throw new Error(`${response.status} ${response.statusText}`);
				}

				return response.json();
			})
			.then(json => {
				work = view(json);
			})
			.catch(error => {
				alert(error);
			});
	}

	function view(data) {
		return data.values.reduce((acc, item) => {
			acc.push({
				id: item.id,
				state: item.state,
				title: item.title,
				author: item.author.user.displayName,
				url: item.links.self[0].href,
			});
			return acc;
		}, []);
	}
</script>

<main>
	<section class="section">
		<div class="container">
			<div class="field">
				<label for="username-input" class="label">Server</label>
				<div class="control">
					<input
						id="username-input"
						class="input"
						type="text"
						bind:value={server}
					/>
				</div>
			</div>
			<div class="field">
				<label for="username-input" class="label">Username</label>
				<div class="control">
					<input
						id="username-input"
						class="input"
						type="text"
						bind:value={username}
					/>
				</div>
			</div>
			<div class="field">
				<label for="token-input" class="label">Token</label>
				<div class="control">
					<input
						id="token-input"
						class="input"
						type="password"
						bind:value={token}
					/>
				</div>
			</div>
			<div class="field">
				<div class="control">
					<button class="button is-link" on:click={load}>Load data</button>
				</div>
			</div>
		</div>
		<div class="container">
			{#each work as item}
				<div class="content">
					<span class="tag">
						{item.state}
					</span>
					<a href={item.url}>{item.title}</a>
					<small>({item.author})</small>
				</div>
			{/each}
		</div>
	</section>
</main>
