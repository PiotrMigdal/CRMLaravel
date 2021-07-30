<template>
  <div id="right">
    <h1>Development CRM</h1>

    <div class="horizontal">
      <img src="/images/horizontal.png" alt="">
    </div>

    <p>
      Lorem ipsum dolor sit amet, consectetur adipisicing elit. Provident aut, officiis est consequuntur hic eveniet voluptates nisi odio mollitia optio. Porro unde, nostrum voluptatibus atque alias consequuntur dignissimos minima quas?
    </p>
    <div class="tasks">
      <h2>Today's Task</h2>
      <div class="add-action">
        <img src="/images/add.png" alt="">
      </div>
      <ul class="tasks-list">
        <li v-for="task in todayTasks" v-bind:key="task.id">
            <div class="info">
              <div class="left">
                <label class="myCheckbox">
                  <input type="checkbox" name="test" :checked="task.completed" @change="updateTodayTask(task.taskId)">
                  <span></span>
                </label>
                <h4>{{task.title}}</h4>
              </div>
              <div class="right">
                <img src="/images/edit.png" alt="">
                <img src="/images/del.png" alt="" @click="delTask(task.taskId)">
              </div>
            </div>
        </li>
      </ul>
    </div>
    <div class="upcoming">
      <div class="add-task">
        <h2>Upcoming</h2>
        <div class="add-action">
          <img src="/images/add.png" alt="">
        </div>
        <form action="" @submit="addUpcomingTask">
          <input type="text" v-model="newTask">
        </form>
        <ul class="tasks-list">
          <li v-for="upcomingTask in upcoming" v-bind:key="upcomingTask.id">
            <div class="info">
              <div class="left">
                <label class="myCheckbox">
                  <input type="checkbox" name="test" :checked="upcomingTask.completed" @change="checkUpcoming(upcomingTask.taskId)">
                  <span></span>
                </label>
                <h4>{{upcomingTask.title}}</h4>
              </div>
              <div class="right">
                <img src="/images/edit.png" alt="">
                <img src="/images/del.png" alt="" @click="delUpcoming(upcomingTask.taskId)">
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>

</template>

<script>
export default {
  data() {
    return {
      todayTasks: [],
      upcoming: [],
      newTask: "",
    };
  },
  created() {
    this.fetchUpcoming();
    this.fetchTodayTasks();
  },
  methods: {
    //** Upcoming Task */
    //Get upcoming task
    fetchUpcoming() {
      fetch('/api/upcoming')
      .then(res => res.json())
      .then(({data}) => {
        this.upcoming = data;

      })
      .catch(err => console.log(err));
    },
    //Add upcoming task
    addUpcomingTask(e) {
      e.preventDefault();
      if(this.upcoming.length > 9) {
        alert("Firstly complete upcomings");
      } else {
        const newTask = {
          title: this.newTask,
          waiting: true,
          taskId: Math.random().toString(36).substring(7),
        };
        //Post Request
        fetch("/api/upcoming", {
          method: "POST",
          headers: {
            "content-type": "application/json",
          },
          body: JSON.stringify(newTask),
        }).then(() => this.upcoming.push(newTask));

        //Clear or Reset new task
        this.newTask = "";
      }

    },

    //Delete Upcoming Task
    delUpcoming(taskId) {
      if(confirm("Are you sure?")) {
        fetch(`/api/upcoming/${taskId}`, {
           method: 'delete',
        }).then((res) => res.json())
        .then(() => {
          this.upcoming = this.upcoming.filter(({taskId: id}) => id !== taskId);
        }).catch(err => console.log(err));
      }
    },
    //Check upcoming task
      checkUpcoming(taskId) {
        if (this.todayTasks.length > 9) {
          alert("Sorry complete existing task");
          windows.location.href = "/";
        } else {
          this.addDailyTask(taskId);

          //Deleted from this task from db
          fetch(`/api/upcoming/${taskId}`, { method: "delete"}).
          then(() => (this.upcoming = this.upcoming.filter(({taskId: id}) =>id !== taskId))
          );
        }
      },
    //** Today Task Method */
    // Get today tasks
    fetchTodayTasks() {
      fetch('/api/dailytask')
      .then((res) => res.json())
      .then(({data}) => {
        this.todayTasks = data;
      })
      .catch((err) => console.log(err));
    },

    //Add daily task
    addDailyTask(taskId) {
      //Get task
      const task = this.upcoming.filter(({ taskId: id }) => id == taskId)[0];
      console.log(task, 'task');
      //Post Request
      fetch('/api/dailytask', {
        method: 'POST',
        headers: {
          "content-type": "application/json",
        },
        body: JSON.stringify(task),
      })
      .then(() =>this.todayTasks.unshift(task))
      .catch((err) => console.log(err));
    },
    //Delete today task
    delTask(taskId){
      if(confirm('Sure?')) {
        fetch(`/api/dailytask/${taskId}`, {
          method: 'delete',
        })
        .then((res) => res.json())
        .then(() => this.todayTasks = this.todayTasks.filter(({taskId: id}) =>id !== taskId))
        .catch((err) => console.log(err))
      }
    },
    //Complete task
    updateTodayTask(taskId) {
      if (confirm('Complete?')) {
        fetch(`/api/dailytask/${taskId}`, {method: 'delete'})
        .then(() => {})
        .then(() => {
          this.todayTasks = this.todayTasks.filter(
            ({ taskId: id }) => id !== taskId
          );
        })
        .catch((err) => console.log(err));
      }
    }
  },
};
</script>

<style>

</style>