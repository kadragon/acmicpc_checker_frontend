<template>
  <div class="py-12">
    <h4 class="text-primary">학생 추가</h4>
    <div class="md-12">
      <div class="row">
        <div class="col-md-4">
          <label for="name" class="form-label">이름</label>
          <input
            class="form-control"
            id="name"
            type="text"
            required
            v-model="name"
          />
        </div>
        <div class="col-md-2">
          <label for="grade" class="form-label">학년</label>
          <input
            class="form-control"
            id="grade"
            type="number"
            required
            v-model="grade"
          />
        </div>
        <div class="col-md-2">
          <label for="class" class="form-label">반</label>
          <input
            class="form-control"
            id="class"
            type="number"
            required
            v-model="cl"
          />
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <label for="email" class="form-label">Email</label>
          <input
            class="form-control"
            id="email"
            type="email"
            required
            v-model="email"
          />
        </div>
        <div class="col-md-6">
          <label for="acmicpcid" class="form-label">Acmicpc ID</label>
          <input
            class="form-control"
            id="acmicpcid"
            type="text"
            required
            v-model="acmicpcID"
          />
        </div>
      </div>

      <button class="btn btn-sm btn-flat btn-success" @click="addAction">
        등록하기
      </button>
      <button class="btn btn-sm btn-flat btn-danger" @click="cancleAction">
        취소
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "addstudent",
  data() {
    return {
      name: "",
      email: "",
      acmicpcID: "",
      grade: 0,
      cl: 0,
    };
  },
  methods: {
    formdataChecker() {
      if (this.name.length < 3) {
        alert("이름을 확인해주세요.");
        return false;
      } else if (this.email.length < 3) {
        alert("이메일을 확인해주세요.");
        return false;
      } else if (this.acmicpcID.length < 3) {
        alert("ID를 확인해주세요.");
        return false;
      } else if (this.grade < 1 || this.grade > 3) {
        alert("학년을 확인해주세요.");
        return false;
      } else if (this.cl < 1 || this.cl > 6) {
        alert("반을 확인해주세요.");
        return false;
      }
      return true;
    },

    async addAction() {
      if (this.formdataChecker()) {
        console.log(this.name, this.email, this.acmicpcID, this.grade, this.cl);
        await axios
          .post("/api/student", {
            rname: this.name,
            email: this.email,
            acmicpc_id: this.acmicpcID,
            grade: this.grade,
            class: this.cl,
          })
          .then((res) => {
            alert("등록에 성공했습니다.", res.data.id);
            this.$emit("addComplete");
          })
          .catch(() => {
            alert("등록에 실패했습니다. 입력값을 확인해주세요.");
          });
      }
    },
    cancleAction() {
      this.$emit("cancleAction");
    },
  },
};
</script>

<style></style>
