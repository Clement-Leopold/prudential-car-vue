<!--
Say Hello World with Vue!
-->
<template>
  <h1>{{ message }}</h1>
  <h3>car model</h3>

  <el-form ref="form" :model="form" label-width="80px">
    <el-form-item label="username">
      <el-input
        autosize
        v-model="form.username"
        maxlength="50"
        show-word-limit
      ></el-input>
    </el-form-item>
    <el-form-item label="until">
      <el-col :span="11">
        <el-date-picker
          type="date"
          placeholder="pick"
          v-model="form.end"
          style="width: 100%"
        ></el-date-picker>
      </el-col>
    </el-form-item>
    <el-form-item label="car_model">
      <el-radio-group v-model="form.model">
        <el-radio label="1">Toyota</el-radio>
        <el-radio label="2">BMW</el-radio>
      </el-radio-group>
    </el-form-item>

    <el-form-item>
      <el-button type="primary" @click="rent">rent</el-button>
      <el-button>cancle</el-button>
    </el-form-item>
  </el-form>
  <h3>car model count</h3>

  <el-table :data="this.posts" style="width: 100%">
    <el-table-column prop="model_name" label="model_name" width="180" />
    <el-table-column prop="stock" label="stock" width="180" />
  </el-table>
  <h3>car rent record</h3>
  <el-table :data="this.rent_records" style="width: 100%">
    <el-table-column prop="model_name" label="model_name" width="180" />
    <el-table-column prop="id" label="id" width="180" />
    <el-table-column prop="username" label="username" width="180" />
    <el-table-column prop="reserve_seconds" label="reserve_days" width="180" />
  </el-table>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      form: {
        username: "",
        end: "",
        model: "",
      },
      message: "car rental!",
      posts: [],
      rent_records: [],
      picked: "",
    };
  },
  methods: {
    async getData() {
      const items = new Map();
      items.set(1, { car_model: 1, car_model_name: "Toyota Camry" });
      items.set(2, { car_model: 2, car_model_name: "BMW 650" });
      try {
        let response = await axios.get("http://localhost:8080/cars/");
        // JSON responses are automatically parsed.
        this.posts = response.data.map((p) => {
          return {
            model_name: items.get(p.carModel).car_model_name,
            stock: p.stock,
          };
        });

        response = await axios.get("http://localhost:8080/cars/rent/records");
        // JSON responses are automatically parsed.
        this.rent_records = response.data.map((p) => {
          return {
            model_name: items.get(p.carModel).car_model_name,
            reserve_seconds: p.reserveSeconds,
            username: p.username,
            id: p.id,
          };
        });
        console.log(this.rent_records);
      } catch (error) {
        console.log(error);
      }
    },
    async rent() {
      try {
        let carRental = {
          username: this.form.username,
          carModel: this.form.model,
          reserveSeconds: Date.parse(this.form.end) - Date.now(),
        };
        console.log(carRental);
        const response = await axios.post(
          "http://localhost:8080/cars/rent",
          JSON.stringify(carRental),
          {
            headers: {
              // Overwrite Axios's automatically set Content-Type
              "Content-Type": "application/json",
            },
          }
        );
        if (response.status != 200) {
          alert(response.data);
        }
        console.log(response);
        // window.location.reload();
      } catch (e) {
        console.log(e);
      }
    },
  },

  created() {
    this.getData();
  },
};
</script>
