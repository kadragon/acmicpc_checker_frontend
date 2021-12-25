<template>
  <div>
    <h4 class="text-info mt-3">
      과제 수행 확인
      <button v-if="isloading" id="isloading" class="btn btn-sm btn-danger">
        확인중...!!
      </button>
    </h4>

    <table class="table table-striped table-hover table-sm align-middle">
      <thead>
        <tr>
          <th class="text-center"></th>
          <th class="text-center">소속</th>
          <th class="text-center">이름</th>
          <th
            class="text-center"
            v-for="problem in assignment.problem_list"
            :key="problem"
          >
            {{ problem }}
          </th>
          <th class="text-center">확인</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(student, idx) in studentData" :key="student.ID">
          <td class="text-center">{{ idx + 1 }}</td>
          <td class="text-center">{{ student.grade }}-{{ student.class }}</td>
          <td class="text-center">{{ student.rname }}</td>
          <td
            class="text-center"
            v-for="problem in assignment.problem_list"
            :key="problem"
          >
            <i
              v-if="
                this.checkData.get(`${student.acmicpc_id}-${problem}`) === 2
              "
              class="fas fa-check text-info"
            />
            <i
              v-else-if="
                this.checkData.get(`${student.acmicpc_id}-${problem}`) === 1
              "
              class="fas fa-check text-warning"
            />
          </td>
          <td class="text-center">
            <a
              class="btn btn-sm btn-outline-dark btn-flat"
              :href="`https://www.acmicpc.net/status?user_id=${student.acmicpc_id}`"
              target="_black"
              >보러가기</a
            >
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "check",
  data() {
    return {
      idx: this.$route.params.id,
      assignment: {
        name: "",
        start_time: "",
        end_time: "",
        end_time_check: "",
        problem_list: [],
      },
      studentData: [],
      checkData: new Map(),
      isloading: true,
      countData: new Map(),
    };
  },
  methods: {
    async getAssignInfo() {
      await axios
        .get(`/api/assignment/${this.idx}`)
        .then((res) => {
          this.getStudentList(res.data.class_info_id);

          this.assignment.name = res.data.name;
          this.assignment.start_time = res.data.start_time.split(" ")[0];
          this.assignment.end_time = res.data.end_time.split(" ")[0];
          this.assignment.end_time_check = new Date(
            res.data.end_time.split(" ")[0],
          );
          this.assignment.problem_list = res.data.problem_list.split(",");
        })
        .catch(() => {
          alert("과목 정보를 가져오는데 실패했습니다. 링크를 확인해주세요.");
        });
    },
    async getStudentList(class_info_id) {
      await axios
        .get(`/api/classInfo/student/${class_info_id}`)
        .then((res) => {
          this.studentData = res.data;
        })
        .catch(() => {
          alert("학생 명단을 가져오는데 실패했습니다. 관리자에게 문의하세요.");
        });
    },
    async getCheckInfo() {
      await axios.get(`/api/acmicpc/check/${this.idx}`).then((res) => {
        let tmp = new Map();
        res.data.forEach((el) => {
          if (
            this.assignment.end_time_check >=
            new Date(el.solved_time.split("T")[0])
          ) {
            tmp.set(`${el.student_a_id}-${el.problem_id}`, 2);
          } else {
            tmp.set(`${el.student_a_id}-${el.problem_id}`, 1);
          }
        });

        this.checkData = tmp;
        this.isloading = false;
      });
    },
  },
  mounted() {
    this.getAssignInfo();
    this.getCheckInfo();

    console.log(this.countData);
  },
};
</script>
