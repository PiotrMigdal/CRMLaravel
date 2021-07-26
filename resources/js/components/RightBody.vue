<template>
  <div id="right">
    <h1>Development CRM</h1>

    <div class="horizontal">
      <img src="/images/horizontal.png" alt="">
    </div>

    <p>
      Lorem ipsum dolor sit amet, consectetur adipisicing elit. Provident aut, officiis est consequuntur hic eveniet voluptates nisi odio mollitia optio. Porro unde, nostrum voluptatibus atque alias consequuntur dignissimos minima quas?
    </p>

    <div class="task">
      <div class="add-task">
        <h2>Today's Task</h2>
        <div class="add-action">
          <img src="/images/add.png" alt="">
        </div>
        <ul class="task-list"></ul>
      </div>
    </div>
  </div>

</template>

<script>
export default {
  data() {
    return {
      todayTask: [],
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
        console.log(data);

      })
      .catch(err => console.log(err));
    },
    //** Today Task Method */
    // Get today tasks
    fetchTodayTasks() {},
  },
};
</script>

<style>

</style>