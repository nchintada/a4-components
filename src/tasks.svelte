<script>
  export let signedOut = false;

  const user1 = null;
  var count = 0;
  const ids = [];

  const getUser = function() {
    const u = fetch('/userpage', {
      method: 'GET',
      headers: {
        "Content-type": "application/json"
      }
    })
    .then(function(response) {
        return response.json();
    })
    .then(function(json) {
      console.log(json)
      const header = document.getElementById("welcome")
      console.log(header)
      const user1 = json.user
      var message = "Hello " + json.user + "!"
      header.appendChild(document.createTextNode(message))
      return json.user
    })
    .then(function(jso) {
      return getTasks(jso)
    })

    return u
  }

  function getTasks(user) {
    console.log(user)
    const t = fetch('/tableData', {
      method: 'GET',
      headers: {
        "Content-type": "application/json"
      }
    })
    .then(function(response) {
        return response.json();
    })
    .then(function(json) {
      console.log("Array: " + JSON.stringify(json))
      return json
    })

    return t
  }

  function addTask(e) {
    e.preventDefault()

    const input = document.querySelector( '#task' )
    const input2 = document.querySelector( '#duedate' )
    const input3 = document.querySelector( '#priority')
    var h = document.getElementById('welcome')
    var name = h.innerHTML
    var ind = name.indexOf("Hello ")
    var uname = name.substring(ind+6,name.length-1)
    const json = { username: uname, task: input.value, duedate: input2.value, priority: input3.value }
    const body = JSON.stringify( json )
    console.log(body)

    promise = fetch( '/add', {
         method:'POST',
         body,
         headers: {
          "Content-type": "application/json"
        }
    })
    .then( response => response.json() )
  }

  function editTask(e) {
    e.preventDefault()

    const todoForm = document.querySelector("form")
    var tdi = ids[ids.length-1]
    console.log("TDI: ", tdi)
    tdi.task = todoForm.elements.task.value
    tdi.duedate = todoForm.elements.duedate.value
    tdi.priority = todoForm.elements.priority.value
    console.log("Modified: ", tdi)

    promise = fetch( '/edit', {
           method:'POST',
           body: JSON.stringify(tdi),
           headers: {
            "Content-type": "application/json"
          }
    })
    .then( response => response.json() )
  }

  function deleteTask(e) {
    e.preventDefault()

    const todoForm = document.querySelector("form")
    var tdi = ids[ids.length-1]

    promise = fetch( '/delete', {
           method:'POST',
           body: JSON.stringify({id: tdi._id}),
           headers: {
            "Content-type": "application/json"
          }
    })
    .then( response => response.json() )
  }

  function setFields(todoItem) {
    const todoForm = document.querySelector("form")
    todoForm.elements.task.value = todoItem.task
    todoForm.elements.duedate.value = todoItem.duedate
    todoForm.elements.priority.value = todoItem.priority
    ids.push(todoItem)
  }

  function signOut(e) {
    e.preventDefault()

    console.log('Going to Sign Out')

    fetch('/signout', {
     method: 'GET',
     headers: {
       "Content-type": "application/json"
     }
    })
    .then( function(response) {
       return response.text();
    })
    .then(function(json) {
     console.log("Bye!")
     signedOut = true
     location.reload()
    })
  }

  let promise = getUser()
</script>

<div class="table" align="center">
  <h1 id="welcome">
  </h1>
  <h2> Your To-Do List </h2>
  <table class="table col-sm-6" align="center" id="todolist">
    <thead class="thead-dark">
      <tr>
        <!-- <th scope="col">#</th> -->
        <th scope="col">Task</th>
        <th scope="col">Due Date</th>
        <th scope="col">Priority</th>
      </tr>
    </thead>
    {#await promise then tasks}
    <tbody>
      {#each tasks as task}
      <tr on:click={setFields(task)}>
        <td type="task">{task.task}</td>
        <td type="duedate">{task.duedate}</td>
        <td type="priority">{task.priority}</td>
      </tr>
      {/each}
    </tbody>
    {/await}
  </table>
  </div>
  <div class="input form" align="center" id="table">
  <h2>Enter User Data</h2>
  <form class="container form-group col-md-4 col-md-offset-4" action="/add" method="POST" align="center">
    <!--<div class="form-group" align="center"> -->
      <div class="form-group mb-2 mr-sm-2" align="left">
        <label for="task">Task</label>
        <input type="text" class="form-control" id="task">
      </div>
      <div class="form-group mb-2 mr-sm-2" align="left">
        <label for="duedate" >Due Date</label>
        <input type="date" class="form-control" id="duedate" align="center">
      </div>
      <div class="form-group mb-2 mr-sm-2" align="left">
        <label for="priority">Priority</label>
        <select class="form-control" id="priority" align="center">
          <option>Low</option>
          <option selected>Normal</option>
          <option>High</option>
        </select>
        <small id="rowtxt" class="form-text text-muted">Click a item to modify or delete it.</small>
      </div>
    <div class="form-group mb-2 mr-sm-2" align="center">
      <button type="submit" class="btn btn-large btn-primary" id="add" align="center" on:click={addTask}>Add</button>
      <button type="submit" class="btn btn-large btn-primary" id="edit" align="center" on:click={editTask}>Edit</button>
      <button type="submit" class="btn btn-large btn-primary" id="delete" align="center" on:click={deleteTask}>Delete</button>
    </div>
  </form>
  </div>
  <div align="center">
  <button type="button" class="btn btn-primary" id="signout" on:click={signOut}>Sign Out</button>
</div>

<style>
  tr:hover{background-color:#EEE}
</style>
