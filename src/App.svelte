<script>
// name variable is defined in main.js
	export let name;


// TODO LIST JS LOGIC
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	let uid = 1;

	let todos = [
		{ id: uid++, done: false, description: 'meal prep' },
		{ id: uid++, done: false, description: 'hang laundry' },
		{ id: uid++, done: true,  description: 'check emails' },
		{ id: uid++, done: false, description: 'mow the lawn' },
		{ id: uid++, done: false, description: 'empty bins' },
		{ id: uid++, done: false, description: 'feed the cat' },
	];

	function add(input) {
		const todo = {
			id: uid++,
			done: false,
			description: input.value
		};

		todos = [todo, ...todos];
		input.value = '';
	}

	function remove(todo) {
		todos = todos.filter(t => t !== todo);
	}

	function mark(todo, done) {
		todo.done = done;
		remove(todo);
		todos = todos.concat(todo);
	}


// TARGET AND BACKLOG 
	let morningTask = 1;
	let afternoonTask = 1;

</script>


<!--HTML MARKUP-->

<!--INPUT NAME FIELD-->
<div class="input">
<input class="nameInput" placeholder="enter your name here" bind:value={name}>
</div>

<!--WELCOME HEADER-->
<main>
	<h1>Welcome {name}!</h1>
	<p>I'm Svelte-List, your one stop productivity assistant.</p> 
	<br/> <p>List your tasks and set targets to help you achieve more each day</p>
</main>


<!--TODO LIST-->
<h1>DAILY PLANNER</h1>

<div class='board'>
	<input
		placeholder="what needs to be done?"
		on:keydown={e => e.key === 'Enter' && add(e.target)}
	>

	<div class='left'>
		<h2>todo</h2>
		{#each todos.filter(t => !t.done) as todo (todo.id)}
			<label
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
			>
				<input type=checkbox on:change={() => mark(todo, true)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">remove</button>
			</label>
		{/each}
	</div>

	<div class='right'>
		<h2>done</h2>
		{#each todos.filter(t => t.done) as todo (todo.id)}
			<label
				class="done"
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
			>
				<input type=checkbox checked on:change={() => mark(todo, false)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">remove</button>
			</label>
		{/each}
	</div>
</div>

<!--TARGET SETTING-->
<div class="targetBlock">
<h2>Goal Setting</h2>
<label>
	<p>Number of tasks I aim to do this morning</p>
	<input type=number bind:value={morningTask} min=0 max=10>
	<input type=range bind:value={morningTask} min=0 max=10>
</label>

<label>
<p>Number of tasks I aim to do this afternoon</p>
	<input type=number bind:value={afternoonTask} min=0 max=10>
	<input type=range bind:value={afternoonTask} min=0 max=10>
</label>

<h2 class="main"><span class="green">Target:</span> To complete <span class="green">{morningTask + afternoonTask}</span> tasks by the end of today</h2>
<h2><span class="red">Backlog:</span> I will have <span class="red">{(todos.length) - (morningTask + afternoonTask)}</span> tasks left to do tomorrow</h2>
</div>



<!--CSS STYLING-->

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		text-align: center;
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
	}

	.input{
  width: 100vw;
  position: relative;
  display: flex;
  flex-flow: column wrap;
  align-items: center;
  margin-bottom: 0;
  padding-bottom: 0;

	}

	.nameInput {
		color: green;
		display: flex;
		flex-align: center;

  border: 1px solid pink
		}

	p {
		margin: 0;
		padding: 0
	}	

.targetBlock{
	margin-left: 1em;
	margin-right: 1em;
	margin-bottom: 2em
}

.targetBlock > h2{
	margin: 1em;
}


	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}


		.board {
		display: grid;
		grid-template-columns: 1fr 1fr;
		grid-gap: 1em;
		max-width: 36em;
		margin: 0 auto;
	}

	.board > input {
		font-size: 1.4em;
		grid-column: 1/3;
	}

	h2 {
		font-size: 2em;
		font-weight: 200;
		user-select: none;
		margin: 0 0 0.5em 0;
	}

	label {
		position: relative;
		line-height: 1.2;
		padding: 0.5em 2.5em 0.5em 2em;
		margin: 0.5em 0 0.5em 0;
		border-radius: 2px;
		user-select: none;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
	}

	input[type="checkbox"] {
		position: absolute;
		left: 0.5em;
		top: 0.6em;
		margin: 0;
	}

	.done {
		border: 1px solid hsl(240, 8%, 90%);
		background-color:hsl(240, 8%, 98%);
		opacity: 0.4;
	}

	button {
		position: absolute;
		top: 0;
		right: 0.2em;
		width: 2em;
		height: 100%;
		background: no-repeat 50% 50% url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%23676778' d='M12,2C17.53,2 22,6.47 22,12C22,17.53 17.53,22 12,22C6.47,22 2,17.53 2,12C2,6.47 6.47,2 12,2M17,7H14.5L13.5,6H10.5L9.5,7H7V9H17V7M9,18H15A1,1 0 0,0 16,17V10H8V17A1,1 0 0,0 9,18Z'%3E%3C/path%3E%3C/svg%3E");
		background-size: 1.4em 1.4em;
		border: none;
		opacity: 0;
		transition: opacity 0.2s;
		text-indent: -9999px;
		cursor: pointer;
	}

	label:hover button {
		opacity: 1;
	}

	.red{
		color: red;
	}

	.green{
		color: green
	}

		
</style>