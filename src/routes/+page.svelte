<script lang="ts">
	import 'ress';

	type Todo = {
		text: string;
		done: boolean;
	};

	type Filter = 'all' | 'active' | 'completed';

	let todos = $state<Todo[]>([]);
	let filter = $state<Filter>('all');
  let filteredTodos = $derived(filterTodos());

  $effect(() => {
    const savedTodos = localStorage.getItem('todos')
    if (savedTodos) todos = JSON.parse(savedTodos);
  });

  $effect(() => {
    localStorage.setItem('todos', JSON.stringify(todos));
  });

  $effect(() => {
    console.log('todos',  localStorage.getItem('todos'));
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

  function remaining() {
    return todos.filter(todo => !todo.done).length;
  }

  function deleteTodo(index) {
    todos = todos.filter((todo, i) => i !== index);
  }
</script>

<div class="page">
	<div class="content">
		<input type="text" placeholder="Add a todo" onkeydown={addTodo} />

		<div class="todos">
			{#each filteredTodos as todo, index}
				<div class="todo" class:completed={todo.done}>
					<input type="text" value={todo.text} oninput={editTodoText} data-index={index} />
					<input type="checkbox" checked={todo.done} onchange={editTodoDone} data-index={index} />
          <button class="delete" onclick={() => deleteTodo(index)}>x</button>
				</div>
			{/each}
		</div>

		<div class="filters">
      <button onclick={() => filter = 'all'} class:active={filter === 'all'}>All</button>
      <button onclick={() => filter = 'active'} class:active={filter === 'active'}>Active</button>
      <button onclick={() => filter = 'completed'} class:active={filter === 'completed'}>Completed</button>
		</div>

    <div class="remaining">
      {remaining()} items left
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
	}

	.todo {
		position: relative;
		background-color: #ddd;
		border-radius: 0.5rem;
		border: 1px solid #ccc;
    transition: opacity 200ms;
	}

  .completed {
    opacity: 0.5;
  }

  .filters, .remaining {
		margin-block-start: 1rem;
  }

	input[type='text'] {
		width: 100%;
		padding: 1rem;

    &:focus {
      outline: none;
    }
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
      border-color: #ccc;
    }

    &.delete {
      position: absolute;
      right: 12%;
      top: 50%;
      translate: 0 -50%;
      padding: 0;
    }
	}
</style>
