<template>
  <div class="container">
    <h1 :class="{ 'text-primary': primaryText }" class="text-center">
      {{ title }}
    </h1>
    <p class="small fw-bold text-center">({{ listLengthCount }})</p>

    <div class="row justify-content-center">
      <div class="col-5">
        <ul class="list-group listContainer">
          <li
            class="list-group-item list-group-item-light d-flex justify-content-between"
            v-for="list in lists"
            v-bind:key="list"
            @dblclick="list.isEdit = !list.isEdit"
            @mouseover="list.showDel = true"
            @mouseout="list.showDel = false"
          >
            <input
              v-if="list.isEdit"
              v-model="list.job"
              type="text"
              @keyup.enter="list.isEdit = false"
              @keyup.esc="list.isEdit = false"
              class="form-control"
            />

            <div v-else class="form-check">
              <input
                class="form-check-input"
                type="checkbox"
                value=""
                :id="`check` + list.id"
                :checked="list.isFinish"
                @change="list.isFinish = !list.isFinish"
              />
              <label class="form-check-label" :for="`check` + list.id">
                <span
                  :class="{ 'text-decoration-line-through': list.isFinish }"
                  >{{ list.job }}</span
                >
              </label>
            </div>

            <button
              class="btn btn-sm btn-outline-danger border-0 py-0"
              :class="{ 'd-none': !list.showDel || list.isEdit }"
              v-on:click="del(list.id)"
            >
              <i class="bi bi-trash"></i>
            </button>
          </li>
        </ul>
      </div>
    </div>

    <form
      class="row justify-content-center mt-3"
      action=""
      v-on:submit.prevent="addlist"
    >
      <div class="col-3">
        <input class="form-control" type="text" v-model="input" required />
      </div>
      <div class="col-2">
        <button class="btn btn-primary w-100">Add task</button>
      </div>
    </form>
    <img v-if="isLight" :src="lightOff" alt="" />
    <img v-else :src="lightOn" alt="" />
    <button class="btn btn-primary" v-if="isLight" @click="switchLight">
      ON
    </button>
    <button class="btn btn-primary" v-else @click="switchLight">OFF</button>
  </div>
</template>

<script>
import Swal from "sweetalert2";
export default {
  data() {
    return {
      title: "List Builder",
      lists: [
        {
          id: "1",
          job: "To Sleep",
          isFinish: true,
          showDel: false,
          isEdit: false,
        },
        {
          id: "2",
          job: "To Eat",
          isFinish: false,
          showDel: false,
          isEdit: false,
        },
        {
          id: "3",
          job: "To Attend Workshop",
          isFinish: true,
          showDel: false,
          isEdit: false,
        },
      ],
      input: null,
      primaryText: true,
      lightOff: "https://www.w3schools.com/js/pic_bulboff.gif",
      lightOn: "https://www.w3schools.com/js/pic_bulbon.gif",
      isLight: false,
    };
  },

  computed: {
    listLengthCount() {
      const unfinishJob = this.lists.filter((cur) => !cur.isFinish);
      const finishJob = this.lists.length - unfinishJob.length;
      return (
        "Finish Job -" + finishJob + " | Unfinish Job -" + unfinishJob.length
      );
    },
  },

  methods: {
    addlist() {
      this.lists = [
        ...this.lists,
        { id: ++this.lists.length + "", job: this.input, isFinish: false },
      ];
      this.input = null;
      console.log(this.lists);
    },

    del(id) {
      Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, delete it!",
      }).then((result) => {
        if (result.isConfirmed) {
          this.lists = this.lists.filter((cur) => cur.id !== id);
          const Toast = Swal.mixin({
            toast: true,
            position: "top-end",
            showConfirmButton: false,
            timer: 2000,
            timerProgressBar: true,
            didOpen: (toast) => {
              toast.addEventListener("mouseenter", Swal.stopTimer);
              toast.addEventListener("mouseleave", Swal.resumeTimer);
            },
          });

          Toast.fire({
            icon: "success",
            title: "deleted successfully",
          });
        }
      });
    },
    switchLight() {
      this.isLight = !this.isLight;
    },
  },
};
</script>

<style>
@import "bootstrap-icons/font/bootstrap-icons.css";
@import "bootstrap/dist/css/bootstrap.min.css";
/* 
.listContainer li:hover .del-btn {
  display: block !important;
} */
</style>
