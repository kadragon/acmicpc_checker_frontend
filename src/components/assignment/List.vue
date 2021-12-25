<template>
  <div>
    <input class="form-control-sm" type="text" v-model="filter" />
  </div>
  <table class="table table-striped table-hover table-sm align-middle">
    <thead>
      <tr class="table-success">
        <th class="text-center">ID</th>
        <th class="text-center">과제명</th>
        <th class="text-center">진행상태</th>
        <th class="text-center">시작일</th>
        <th class="text-center">종료일</th>
        <th class="text-center">점검</th>
        <th class="text-center">수정</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="{ ID, name, start_time, end_time } in assigns.filter(
          (assign) => assign.name.indexOf(filter) > -1,
        )"
        :key="ID"
      >
        <td class="text-center">{{ ID }}</td>
        <td>{{ name }}</td>
        <td
          class="text-center"
          v-if="statusChecker(start_time, end_time) == -1"
        >
          <button class="btn btn-sm btn-flat btn-outline-default" disabled>
            대기
          </button>
        </td>
        <td class="text-center" v-if="statusChecker(start_time, end_time) == 0">
          <button class="btn btn-sm btn-flat btn-outline-primary" disabled>
            진행중
          </button>
        </td>
        <td class="text-center" v-if="statusChecker(start_time, end_time) == 1">
          <button class="btn btn-sm btn-flat btn-outline-warning" disabled>
            종료
          </button>
        </td>
        <td class="text-center">{{ start_time.split(" ")[0] }}</td>
        <td class="text-center">{{ end_time.split(" ")[0] }}</td>
        <td class="text-center">
          <router-link :to="`/assignment/${ID}`">
            <button class="btn btn-sm btn-flat btn-info">점검</button>
          </router-link>
        </td>
        <td class="text-center">
          <button class="btn btn-sm btn-flat btn-warning">수정</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import axios from "axios";

export default {
  name: "assignment-list",
  data() {
    return {
      assigns: [],
      filter: "",
    };
  },
  props: ["addedcnt"],
  watch: {},
  mounted() {
    this.getAssigns();
  },
  methods: {
    async getAssigns() {
      await axios.get("/api/assignment").then((res) => {
        this.assigns = res.data;
      });
    },
    statusChecker(start, end) {
      const startTime = new Date(start);
      const endTime = new Date(end);
      const nowTime = new Date();

      if (nowTime < startTime) {
        return -1;
      } else if (nowTime > endTime) {
        return 1;
      } else {
        return 0;
      }
    },
  },
};
</script>

<style></style>
