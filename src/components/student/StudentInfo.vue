<template>
  <div class="list" v-if="view == 'list'">
    <h4 class="text-info">학생 관리</h4>
    <button
      v-if="view == 'list'"
      @click="view = 'add'"
      class="btn btn-sm btn-flat btn-primary pull-right"
    >
      추가하기
    </button>
    <table class="table table-striped table-hover">
      <thead>
        <tr class="">
          <th scope="col" class="text-center">소속</th>
          <th scope="col" class="text-center">이름</th>
          <th scope="col" class="text-center">Solved</th>
          <th scope="col" class="text-center">Acmicpc_ID</th>
          <th scope="col" class="text-center">Email</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="student in students" :key="student.ID">
          <td class="text-center">{{ student.grade }}-{{ student.class }}</td>
          <td class="text-center" scope="row">
            {{ student.rname }}
          </td>
          <td class="text-end">
            {{ student.solved }}
          </td>
          <td>
            {{ student.acmicpc_id }}
          </td>
          <td>{{ student.email }}</td>
        </tr>
      </tbody>
    </table>
  </div>

  <addstudent
    v-if="view == 'add'"
    @addComplete="addComplete"
    @cancleAction="cancleAction"
  />
</template>

<script>
import axios from "axios";
import addstudent from "./AddStudent.vue";

export default {
  name: "StudentInfo",
  components: { addstudent },
  data: function() {
    return {
      students: [],
      view: "list",
    };
  },
  methods: {
    getStudents: async function() {
      const res = await axios.get("/api/student");

      res.data.sort(function(a, b) {
        if (a.grade < b.grade) {
          return -1;
        } else if (a.grade == b.grade) {
          return a.class < b.class ? -1 : 1;
        } else {
          return 1;
        }
      });

      this.students = res.data;
    },
    addComplete: function() {
      this.view = "list";
    },
    cancleAction: function() {
      this.view = "list";
    },
  },
  mounted() {
    this.getStudents();
  },
};
</script>

<style></style>
