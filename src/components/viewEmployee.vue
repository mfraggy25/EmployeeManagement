<template>
  <div id="view-employee">
    <ul class="collection with-header">
      <li class="collection-header">
        <h4>{{ name }}</h4>
      </li>
      <li class="collection-item">Employee ID#: {{ employee_id }}</li>
      <li class="collection-item">Department: {{ dept }}</li>
      <li class="collection-item">Position: {{ position }}</li>
      <li class="collection-item">Date Started: {{ date_started }}</li>
      <li class="collection-item">Email: {{ email }}</li>
      <li class="collection-item">Phone Number: {{ number }}</li>
    </ul>
    <router-link to="/" class="btn grey">Back</router-link>
    <button @click="deleteEmployee" class="btn red">Delete</button>
    <div class="fixed-action-btn">
      <router-link
        v-bind:to="{
          name: 'edit-employee',
          params: { employee_id: employee_id }
        }"
        class="btn-floating btn-large red"
      >
        <i class="fa fa-pencil"></i>
      </router-link>
    </div>
  </div>
</template>

<script>
import db from "./firebaseInit";
export default {
  name: "view-employee",
  data() {
    return {
      employee_id: null,
      name: null,
      dept: null,
      position: null,
      date_started: null,
      email: null,
      number: null
    };
  },
  beforeRouteEnter(to, from, next) {
    db.collection("employees")
      .where("employee_id", "==", to.params.employee_id)
      .get()
      .then(querySnapshot => {
        querySnapshot.forEach(doc => {
          next(vm => {
            vm.employee_id = doc.data().employee_id;
            vm.name = doc.data().name;
            vm.dept = doc.data().dept;
            vm.position = doc.data().position;
            vm.date_started = doc.data().date_started;
            vm.email = doc.data().email;
            vm.number = doc.data().number;
          });
        });
      });
  },
  watch: {
    $route: "fetchData"
  },
  methods: {
    fetchData() {
      db.collection("employees")
        .where("employee_id", "==", this.$route.params.employee_id)
        .get()
        .then(querySnapshot => {
          querySnapshot.forEach(doc => {
            this.employee_id = doc.data().employee_id;
            this.name = doc.data().name;
            this.dept = doc.data().dept;
            this.position = doc.data().position;
            this.date_started = doc.data().date_started;
            this.email = doc.data().email;
            this.number = doc.data().number;
          });
        });
    },
    deleteEmployee() {
      if (confirm("Are you sure?")) {
        db.collection("employees")
          .where("employee_id", "==", this.$route.params.employee_id)
          .get()
          .then(querySnapshot => {
            querySnapshot.forEach(doc => {
              doc.ref.delete();
              this.$router.push("/");
            });
          });
      }
    }
  }
};
</script>
