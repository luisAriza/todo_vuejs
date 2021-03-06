<template lang="pug">
Navbar
.home.w-full.px-2
  .flex.items-center.justify-center.gap-4.pt-8
    img(src="../assets/project.svg" width="140" alt="logo")
    h1(class="sm:block hidden font-bold text-3xl uppercase") Estate Task
  section.tasks
    .info
      .info__title.text-start
        p.text-xl.font-bold Immediate Steps
        p.text-gray-400 These are your immediate steps to complete
      .info__task-left.text-start.mt-4.mb-6
        p.text-slate-500.bg-blue-50.px-4.py-1.w-max.rounded-md
          span.text-blue-400 {{ tasksUncompleted }}
          |  / {{ tasksCreated.length }} tasks left
    FilterTask
    AddTask
    TaskList
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import FilterTask from "@/components/FilterTaskComponent.vue";
import AddTask from "@/components/AddTaskComponent.vue";
import TaskList from "@/components/TaskList.vue";

export default {
  name: "HomeView",
  components: {
    Navbar,
    FilterTask,
    AddTask,
    TaskList,
  },
  data() {
    return {
      search: "",
      searchTags: [],
      tasksCreated: [],
      tags: this.$store.state.tags,
    };
  },
  computed: {
    tasksRecords() {
      let tasks = localStorage.getItem("tasks");

      return JSON.parse(tasks);
    },
    tasksRecordsUser() {
      let user = localStorage.getItem("user");
      let tasksRecords = this.tasksRecords;

      // Index(posición en el array) del user a identificar en tareas registradas
      let tasksIndexUser = tasksRecords.findIndex((v) => v.user == user);
      // User seleccionado en tareas registradas
      if (user != null || undefined) {
        let tasksUser = tasksRecords[tasksIndexUser].tasks;

        return tasksUser;
      }
    },
    searchFilter() {
      return (item) => {
        if (this.search) {
          return (
            item.title.toLowerCase().includes(this.search.toLowerCase()) ||
            item.description.toLowerCase().includes(this.search.toLowerCase())
          );
        } else {
          return item.tags
            .sort()
            .join("")
            .includes(this.searchTags.sort().join(""));
        }
      };
    },
    tasksList() {
      return this.tasksCreated.filter(this.searchFilter);
    },
    tasksUncompleted() {
      return this.tasksCreated.filter((task) => {
        return !task.completed;
      }).length;
    },
  },
  created() {
    if (localStorage.getItem("tasks") != null) {
      this.tasksCreated = this.tasksRecordsUser;
    }
  },
};
</script>

<style scoped lang="postcss">
.tasks {
  @apply grid w-full max-w-[50rem] mx-auto mb-10 p-6 rounded-lg shadow-md border bg-white;
}
.info {
  @apply sm:flex sm:justify-between sm:pb-4 px-2 sm:px-4 border-b;
}
</style>
