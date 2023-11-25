<script lang="ts">
	import 'ress';

	let todos = $state([
		{
			text: 'Learn Svelte',
			done: false
		},
		{
			text: 'Learn German',
			done: true
		}
	]);

	function addTodo(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const input = event.target as HTMLInputElement;
    const text = input.value;
    const done = false;

    todos = [...todos, { text, done }];
	}
</script>

<div class="page">
	<div class="content">
		<input type="text" placeholder="Add a todo" onkeydown={addTodo} />

		<div class="todos">
			{#each todos as todo, i}
				<div class="todo">
					<input type="text" value={todo.text} />
					<input type="checkbox" checked={todo.done} />
				</div>
			{/each}
		</div>
	</div>
</div>

<style>
	.page {
		background-color: #fafafa;
		height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.content {
		padding: 1rem;
		border-radius: 1rem;
		background-color: #eee;
		box-shadow:
			0.5rem 0.5rem 1rem #ccc,
			-0.5rem -0.5rem 1rem #ddd;
		max-width: 800px;
		border: 1px solid #bbb;
	}

	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}

	.todo {
		position: relative;
		background-color: #eee;
		border-radius: 0.5rem;
		border: 1px solid #ddd;
	}

	input[type='text'] {
		width: 100%;
		padding: 1rem;
	}

	input[type='checkbox'] {
		position: absolute;
		right: 4%;
		top: 50%;
		translate: 0 -50%;
	}
</style>
