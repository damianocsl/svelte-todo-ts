<script lang="ts">
	import 'ress';

	type Todo = {
		text: string;
		done: boolean;
	};

	type Filter = 'all' | 'active' | 'completed';

	let todos = $state<Todo[]>([
		{ text: 'Learn Svelte', done: false },
		{ text: 'Learn German', done: true }
	]);

	let filter = $state<Filter>('all');
  let filteredTodos = $derived(filterTodos());

  $effect(() => {
    console.log('todos', todos)
    console.log('filter', filter)
    console.log('filteredTodos', filteredTodos)
  });

	function addTodo(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const input = event.target as HTMLInputElement;
		const text = input.value;
		const done = false;

		todos = [...todos, { text, done }];
	}

	function editTodoText(event: Event) {
		const input = event.target as HTMLInputElement;
		const index = Number(input.dataset.index);
		todos[index].text = input.value;
	}

	function editTodoDone(event: Event) {
		const input = event.target as HTMLInputElement;
		const index = Number(input.dataset.index);
		todos[index].done = input.checked;
	}

  function setFilter(newFilter: Filter) {
    filter = newFilter;
  }

  function filterTodos() {
    switch (filter) {
      case 'active':
        return todos.filter(todo => !todo.done);
      case 'completed':
        return todos.filter(todo => todo.done);
      default:
        return todos;
    }
  }
</script>

<div class="page">
	<div class="content">
		<input type="text" placeholder="Add a todo" onkeydown={addTodo} />

		<div class="todos">
			{#each filteredTodos as todo, i}
				<div class="todo">
					<input type="text" value={todo.text} oninput={editTodoText} data-index={i} />
					<input type="checkbox" checked={todo.done} onchange={editTodoDone} data-index={i} />
				</div>
			{/each}
		</div>

		<div class="filters">
      <button onclick={() => setFilter('all')}>All</button>
      <button onclick={() => setFilter('active')}>Active</button>
      <button onclick={() => setFilter('completed')}>Completed</button>
		</div>
	</div>
</div>

<style lang="scss">
	.page {
		background-color: #fafafa;
		height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center;
    font-family: sans-serif;
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
		margin-block-end: 1rem;
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

	button {
		border: 1px solid #ddd;
		border-radius: 0.5rem;
		padding: 0.5rem 1rem;

    &:not(:last-child) {
      margin-right: 0.5rem;
    }

    &.active {
      background-color: #ddd;
    }
	}
</style>
