<script>
  import { TasksCollection } from '../api/TasksCollection';
  import { useTracker } from 'meteor/rdb:svelte-meteor-data';
  import Task from './Task.svelte';
  import TaskForm from './TaskForm.svelte';

  let hideCompleted = false;

  const hideCompletedFilter = { isChecked: { $ne: true } };

  $m:tasks = TasksCollection.find(hideCompleted ? hideCompletedFilter : {}, { sort: { createdAt: -1 } }).fetch()

  let incompleteCount;
  let pendingTasksTitle = '';

  $: {
    incompleteCount = useTracker(() => TasksCollection.find(hideCompletedFilter).count());
    pendingTasksTitle = `${
      $incompleteCount ? ` (${$incompleteCount})` : ''
    }`;
  }

  const setHideCompleted = value =>  {
    hideCompleted = value;
  }
</script>


<div class="app">
    <header>
        <div class="app-bar">
            <div class="app-header">
                <h1>📝️ To Do List {pendingTasksTitle}</h1>
            </div>
        </div>
    </header>

    <div class="main">
        <TaskForm />
        <div class="filter">
            <button on:click={() => setHideCompleted(!hideCompleted)}>
                {hideCompleted ? 'Show All' : 'Hide Completed'}
            </button>
        </div>
        <ul class="tasks">
          {#each tasks as task}
              <Task key={task._id} task={task} />
          {/each}
        </ul>
    </div>
</div>
