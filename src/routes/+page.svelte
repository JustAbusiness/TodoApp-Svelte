<script>
// @ts-nocheck

    let todos = [];
    let task = "";
    let error = "";
   
    const addTodo = () => {
        let todo = {
        task: task,
        isComplete: false,
        createAt: new Date(),
    };
        if(task !== "") {                    // Kiểm tra nếu mà nhấn phím Enter thì phải hiện ra data
            // @ts-ignore
            todos = [todo, ...todos];
        } else {
            error = " Task is not added ";
        }

        task = "";              // Khi nhập input rùi thì nó sẽ nhắc lệnh nhập vào 
    };
    
    // $: console.table(todos)

    const markTodo = (index) => {
        todos[index].isComplete =  !todos[index].isComplete ;    // Nếu complete rui thì bấm lần nữa sẽ bỏ dấu complete     
    };

    const markUndo = (index) => {
        let deleteItem = todos[index]
        let newTodos = todos.filter((item1) => item1 != deleteItem)         // Remove todos trc do = filter
        todos = newTodos
    };

    const keyIsPressed = (event) => {                   //  KIỂM ADD TỪ BÀN PHÍM THAY VÌ NHẤN NÚT
        if(event.key == 'Enter') {
            addTodo();
        };
    };

</script>



<!-- HTML STYLE -->

<input type="text" placeholder="Add a task" bind:value={task}>
<button on:click={addTodo}>Add</button>

<ol>
    {#each todos as item, index}
        <li class:complete={item.isComplete}>
            <span>
                {item.task}
            </span>

            <span>
               <button on:click={() => markTodo(index)}> ✔ </button> 
            </span>

            <span>
                <button on:click={() => markUndo(index)}> ✕ </button> 
                
            </span>
        </li>

    {:else}
        <p> No todos found </p>
        <p class="error"> {error}</p>

    {/each}

</ol>
    
    <!-- Chức năng gõ phím enter để Add thay vì nhấn nút -->
    <svelte:window  on:keydown={keyIsPressed}/>

<!-- CSS STYLE -->
<style>
    .complete {
        text-decoration: line-through;       
    }

    .error {
        color: red;
    }

</style>
