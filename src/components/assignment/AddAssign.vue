<template>
  <!-- Button trigger modal -->
  <button
    type="button"
    class="btn btn-flat btn-sm btn-info pull-right"
    data-bs-toggle="modal"
    data-bs-target="#assignAdd"
  >
    추가하기
  </button>

  <!-- Modal -->
  <div class="modal fade" id="assignAdd" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">과제 추가하기</h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row mb-3">
            <div class="col-md-12">
              <label for="name" class="form-label">과제명</label>
              <input
                class="form-control"
                id="name"
                type="text"
                required
                v-model="name"
              />
            </div>
          </div>
          <div class="row mb-3">
            <div class="col-md-12">
              <label for="classinfo" class="form-label">대상 반</label>
              <select class="form-select" v-model="class_info_id">
                <option :value="null" disabled selected></option>
                <option
                  v-for="{
                    ID,
                    year,
                    semester,
                    subject,
                    class_count,
                  } in class_list"
                  :key="ID"
                  :value="ID"
                >
                  {{ `${year}-${semester} ${subject} - ${class_count}반` }}
                </option>
              </select>
            </div>
          </div>
          <div class="row mb-3">
            <div class="col-md-6">
              <label for="startTime" class="form-label">시작일</label>
              <input
                class="form-control"
                id="startTime"
                type="date"
                required
                v-model="start_time"
                default="{{start_time}}"
              />
            </div>
            <div class="col-md-6">
              <label for="endTime" class="form-label">종료일</label>
              <input
                class="form-control"
                id="endTime"
                type="date"
                required
                v-model="end_time"
              />
            </div>
          </div>

          <div class="row mb-3">
            <div class="col-md-12">
              <label for="problemList" class="form-label">문제목록</label>
              <div class="input-group">
                <input
                  class="form-control"
                  id="problemList"
                  type="text"
                  required
                  v-model="problem_list"
                  placeholder="문제집 고유번호를 입력하세요."
                />
                <span class="input-group-text" @click="getProblemList"
                  >가져오기</span
                >
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-primary" @click="addAssignment">
            저장하기
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "add",
  data() {
    return {
      class_info_id: null,
      name: "프로그래밍 I 복습과제 - 1차 - 2반",
      assignment_count: 0,
      start_time: new Date().toISOString().slice(0, 10),
      end_time: "",
      problem_list: "",
      class_list: [],
    };
  },
  computed: {},
  watch: {
    start_time() {
      const start = new Date(this.start_time);
      this.end_time = new Date(start.setDate(start.getDate() + 6))
        .toISOString()
        .slice(0, 10);
    },
  },
  mounted() {
    this.getClassList();
  },
  methods: {
    async getProblemList() {
      await axios
        .get(`/api/acmicpc/convert/${this.problem_list}`)
        .then((res) => {
          this.problem_list = res.data.problemList;
        })
        .catch(() => {
          alert("뭔가 잘못되었습니다. 문제집 번호만 넣어주세요.");
        });
    },
    async getClassList() {
      await axios
        .get(`/api/classInfo`)
        .then((res) => {
          this.class_list = res.data;
        })
        .catch(() => {
          alert("반 정보를 가져오는데 실패했습니다.");
        });
    },
    async addAssignment() {
      await axios
        .post("/api/assignment", {
          class_info_id: this.class_info_id,
          name: this.name,
          assignment_count: 0,
          start_time: this.start_time,
          end_time: this.end_time,
          problem_list: this.problem_list,
        })
        .then((res) => {
          alert(`${res.data.ID}번째 과제로 잘 등록 되었습니다.`);
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style></style>
