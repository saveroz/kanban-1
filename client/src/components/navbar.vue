<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <a class="navbar-brand" href="#">X Kanban Board</a>

      <button
        class="navbar-toggler"
        type="button"
        data-toggle="collapse"
        data-target="#navbarNav"
        aria-controls="navbarNav"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav"></div>
      <div class="mx-auto">
        <form class="form-inline">
          <input
            class="form-control mr-sm-2"
            type="search"
            placeholder="Search"
            aria-label="Search"
            v-model="search"
          />
        </form>
      </div>
      <div class="ml-auto">
        <b-button v-b-modal.modal-task class="btn btn-blue-grey">New Task</b-button>
      </div>
    </nav>

    <b-modal id="modal-task">
      <b-form @submit.prevent="createNewTask" id="createTaskForm">
        <b-form-group label="Enter title: " label-for="title">
          <b-form-input type="text" v-model="title" placeholder="Enter title" id="title" required></b-form-input>
        </b-form-group>
        <b-form-group label="Assign to: " label-for="person">
          <b-form-input type="text" v-model="assigned" placeholder="assigned to" id="person"></b-form-input>
        </b-form-group>
        <b-form-group label="Description: " label-for="description">
          <b-form-textarea v-model="description" placeholder="description" rows="3" max-rows="6"></b-form-textarea>
        </b-form-group>
      </b-form>
      <div slot="modal-footer">
        <b-button
          variant="primary"
          type="submit"
          form="createTaskForm"
          class="btn btn-blue-grey"
        >Confirm</b-button>
      </div>
    </b-modal>
  </div>
</template>

<script>
import db from "../api/firestore";
import Vue from "vue";

export default {
  name: "navbar",
  data() {
    return {
      title: "",
      description: "",
      assigned: "",
      status: 0,
      search:""
    };
  },
  methods: {
    createNewTask() {
      // console.log("aselole");
      Vue.swal.fire({
        title: "Creating your task...",
        allowOutsideClick: () => !Vue.swal.isLoading()
      });
      Vue.swal.showLoading();

      let newTask = {
        title: this.title,
        description: this.description,
        assigned: this.assigned,
        status: this.status
      };
      db.collection("task")
        .add(newTask)
        .then(ref => {
          console.log("Added document with ID: ", ref.id);
          this.$bvModal.hide("modal-task");
          this.title = "";
          this.description = "";
          this.assigned = "";
          Vue.swal.close();
          Vue.swal.fire({
            type: "success",
            title: "You Have Created task !",
            showConfirmButton: false,
            timer: 1500
          });
        });
    }
  },
  watch : {
    search(newValue, oldValue){
      // console.log(oldValue)
      // console.log(newValue)
      this.$emit("forSearch", newValue)
    }
  }
};
</script>

<style>
</style>
