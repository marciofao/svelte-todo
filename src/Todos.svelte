<script>
  import { initializeApp } from "firebase/app";
  import {
    getFirestore,
    collection,
    onSnapshot,
    doc,
    addDoc,
    updateDoc,
    deleteDoc,
  } from "firebase/firestore";
  import { firebaseConfig } from "./firebaseConfig.js";

  //initialize firebase
  const firebase = initializeApp(firebaseConfig);
  //initialize Cloud Firestore and get a reference to the service
  const db = getFirestore(app);
  //read data from database
  const colRef = collection(db, "todos");

  //an array of todos, where all our todo is been created
  let todos = [];

  const unsubscribe = onSnapshot(colRef, (querySnapshot) => {
    let dbTodos = [];
    querySnapshot.forEach((doc) => {
      let todo = { ...doc.data(), id: doc.id };
      dbTodos = [todo, ...dbTodos];
    });
    todos = dbTodos;
  });

  let task = "";

  //the onclick event that will fire, when the button is clicked
  const addTodo = async () => {
    if (task !== "") {
      const docRef = await addDoc(collection(db, "todos"), {
        task: task,
        isDone: false,
        Date: new Date(),
      });
    }
    task = "";
  };

  const keyPressed = (event) => {
    if (event.key === "Enter") {
      addTodo();
    }
  };
  const markAsDone = async (item) => {
    await updateDoc(doc(db, "todos", item.id), {
      isDone: !item.isDone,
    });
  };

  const delTodo = async (id) => {
    await deleteDoc(doc(db, "todos", id));
  };
</script>

<input type="text" placeholder="add your new task" bind:value={task} />
<button on:click={addTodo}>Add Todo</button>

<!-- for each loop to loop through our available todo in the todos array -->
{#each todos as item}
  <li class:done={item.isDone}>
    <span class="items">{item.task}</span>
    <span><button on:click={() => markAsDone(item)}>Done</button></span>
    <span><button on:click={() => delTodo(item.id)}>Delete</button></span>
  </li>
{:else}
  <p>No Task Found, schedule your todo task above.</p>
{/each}

<svelte:window on:keydown={keyPressed} />

<style>
  button {
    background-color: aliceblue;
    margin: 5px 5px;
    border: 1px solid #4472c4;
    text-decoration: none;
    font-size: 16px;
    width: 150px;
    color: blue;
    transition: 0.4s;
    cursor: pointer;
  }
  button:hover {
    background-color: blue;
    color: white;
  }
  input {
    border: 1px solid blue;
    color: darkblue;
  }
  ::placeholder {
    color: gray;
  }
  .items {
    margin-right: 70px;
    width: 150px;
    display: inline-flex;
  }
  li button {
    background-color: #fff;
    margin: 5px 5px;
    border: 1px solid #4472c4;
    text-decoration: none;
    font-size: 14px;
    width: 70px;
    color: #4472c4;
    transition: 0.9s;
    cursor: pointer;
  }
  li button:hover {
    background-color: #4472c4;
    color: white;
  }
  .done {
    text-decoration: line-through;
  }
</style>
