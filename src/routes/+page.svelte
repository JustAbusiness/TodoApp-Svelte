<script>
    
// @ts-nocheck

    let todos = [];
    let task = "";
    let error = "";
   
    const addTodo = async() => {
        if(task !== "") {                    // Kiểm tra nếu mà nhấn phím Enter thì phải hiện ra data
            // @ts-ignore
           // Add a new document with a generated id.
            const docRef = await addDoc(collection(db, "todos"), {
                task: task,
                isComplete: false,
                createAt: new Date(),
            });
        } else {
            error = " Task is not added ";
        }

        task = "";              // Khi nhập input rùi thì nó sẽ nhắc lệnh nhập vào 
    };
    
    $: console.table(todos)


    const markTodo = async(item) => {
        // todos[index].isComplete =  !todos[index].isComplete ;    // Nếu complete rui thì bấm lần nữa sẽ bỏ dấu complete       
        // Set the "capital" field of the city 'DC'
        await updateDoc(doc(db, "todos", item.id), {
            isComplete: !item.isComplete
        });
    };

    const markUndo = async(id) => {
        // let deleteItem = todos[index]
        // let newTodos = todos.filter((item1) => item1 != deleteItem)         // Remove todos trc do = filter
        // todos = newTodos

        await deleteDoc(doc(db, "todos", id));

    };

    const keyIsPressed = (event) => {                   //  KIỂM ADD TỪ BÀN PHÍM THAY VÌ NHẤN NÚT
        if(event.key == 'Enter') {
            addTodo();
        };
    };


 // FIREBASE CONFIG
import { initializeApp, getApp, getApps } from "firebase/app";
import { getFirestore,collection, onSnapshot, updateDoc, doc, deleteDoc, addDoc} from "firebase/firestore";
import {firebaseConfig} from "$lib/firebaseConfig.js";
import {browser} from "$app/environment";

let firebaseApp;
let db;

if (browser) {
    if(getApps().length === 0) {
        // Initialize Firebase
    firebaseApp = initializeApp(firebaseConfig);
    } else {
        getApp()
    }   
    // Initialize Cloud Firestore and get a reference to the service
    db = getFirestore();
}

const colRef = browser &&  collection(db, "todos");

const unsubscribe = browser && onSnapshot(colRef, (querySnapshot) => {
    let fbTodos = [];
  querySnapshot.forEach((doc) => {
    let todo = {...doc.data(), id: doc.id}
    fbTodos = [todo, ...fbTodos];
  });
    todos = fbTodos;
});

console.log({firebaseApp, db});

</script>



<!-- HTML STYLE -->

<input type="text" placeholder="Add a task" bind:value={task}>
<button on:click={addTodo}>Add</button>

<ol>
    {#each todos as item}
    
        <li class:complete={item.isComplete}>
            <span>
                {item.task}
            </span>

            <span>
               <button on:click={() => markTodo(item)}> ✔ </button> 
            </span>

            <span>
                <button on:click={() => markUndo(item.id)}> ✕ </button> 
                
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
