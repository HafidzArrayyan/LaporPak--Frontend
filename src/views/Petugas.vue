<template>
  <div>
    <div class="row">
      <div class="col">
        <div class="card">
          <h3
            class="card-header d-flex justify-content-between align-items-center"
          >
            Data Petugas
            <button
              class="btn btn-icon btn-success"
              type="button"
              v-b-modal.modalPetugas
              v-on:click="Add"
            >
              <span class="btn-inner--icon"><i class="fa fa-plus"></i></span>
              <span class="btn-inner--text">Tambah</span>
            </button>
          </h3>

          <!-- Light table -->
          <div class="table-responsive">
            <b-table
              striped
              hover
              :items="petugas"
              :fields="fields"
              class="table align-items-center table-flush"
            >
              <template v-slot:cell(level)="data">
                <!-- <b-badge variant="default">{{ data.item.level }}</b-badge> -->
                <b-badge
                  class="text-dark"
                  variant="info"
                  v-if="data.item.level === 'admin'"
                  >{{ data.item.level }}</b-badge
                >
                <b-badge
                  variant="default"
                  v-if="data.item.level === 'petugas'"
                  >{{ data.item.level }}</b-badge
                >
              </template>
              <template v-slot:cell(Aksi)="data">
                <b-button
                  size="sm"
                  v-on:click="Edit(data.item.id)"
                  v-b-modal.modalPetugas
                  class="btn btn-outline-warning"
                  ><i class="fas fa-pen"></i> Ubah</b-button
                >&nbsp;
                <b-button
                  size="sm"
                  v-on:click="Drop(data.item.id)"
                  class="btn btn-danger"
                  ><i class="fas fa-trash"></i> Hapus</b-button
                >
              </template>
            </b-table>
            <div class="text-center my-3">
              <b-badge
                ><h5 class="text-success">{{ message }}</h5></b-badge
              >
            </div>
            <b-pagination
              v-model="currentPage"
              :per-page="perPage"
              :total-rows="rows"
              align="center"
              v-on:input="pagination"
            >
            </b-pagination>
          </div>
        </div>
      </div>
    </div>
    <b-modal id="modalPetugas" @ok="Save">
      <template v-slot:modal-title> Form Data Petugas </template>
      <form ref="form">
        <div class="form-group">
          <label for="nama" class="form-control-label">Nama Patugas</label>
          <input
            type="text"
            v-model="nama"
            name="nama"
            class="form-control"
            id="nama"
            placeholder="Nama Lengkap"
          />
        </div>
        <div class="form-group">
          <label for="nik" class="form-control-label">NIK</label>
          <input
            type="number"
            v-model="nik"
            name="nik"
            class="form-control"
            id="nik"
            placeholder="NIK"
          />
        </div>
        <div class="form-group">
          <label for="username" class="form-control-label">Username</label>
          <input
            type="text"
            v-model="username"
            name="username"
            class="form-control"
            id="username"
            placeholder="Username"
          />
        </div>
        <div class="form-group">
          <label for="password" class="form-control-label">Password</label>
          <input
            type="text"
            v-model="password"
            name="password"
            class="form-control"
            id="password"
            placeholder="Password"
          />
        </div>
        <div class="form-group">
          <label for="telp" class="form-control-label">Telepon</label>
          <input
            type="number"
            v-model="telp"
            name="telp"
            class="form-control"
            id="telp"
            placeholder="Telepon"
          />
        </div>
        <div class="form-group">
          <label for="level" class="form-control-label">Level</label>
          <select class="form-control" name="level" id="level" v-model="level">
            <option value="admin" checked>Admin</option>
            <option value="petugas">Petugas</option>
            <!-- <option value="masyarakat">Masyarakat</option> -->
          </select>
        </div>
      </form>
    </b-modal>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      search: "",
      id: "",
      nama: "",
      nik: "",
      username: "",
      password: "",
      telp: "",
      level: "",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      token: "",
      petugas: [],
      fields: ["id", "nama", "username", "telp", "level", "Aksi"],
    };
  },

  methods: {
    getData: function () {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.message = "Mohon tunggu...";
      this.axios
        .get("/petugas/" + this.perPage + "/" + offset, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.message = "";
            this.petugas = response.data.data.user;
            this.rows = response.data.data.count;
          } else {
            this.message = "Gagal menampilkan data petugas.";
            this.$router.push({ name: "login" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    pagination: function () {
      if (this.search == "") {
        this.getData();
      } else {
        this.searching();
      }
    },

    Add: function () {
      this.action = "insert";
      this.nama = "";
      this.nik = "";
      this.username = "";
      this.password = "";
      this.level = "";
      this.telp = "";
    },

    Edit: function (id) {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      this.axios
        .get("/petugas/" + id, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.action = "update";
            this.id = response.data.data.user[0].id;
            this.nama = response.data.data.user[0].nama;
            this.username = response.data.data.user[0].username;
            this.nik = response.data.data.user[0].nik;
            this.password = "";
            this.level = response.data.data.user[0].level;
            this.telp = response.data.data.user[0].telp;
          } else {
            this.message = "Gagal menampilkan data petugas.";
            this.$router.push({ name: "petugas" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    Save: function () {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      this.$bvToast.show("loadingToast");
      if (this.action === "insert") {
        let form = new FormData();
        form.append("id", this.id);
        form.append("nama", this.nama);
        form.append("nik", this.nik);
        form.append("username", this.username);
        form.append("password", this.password);
        form.append("level", this.level);
        form.append("telp", this.telp);

        this.axios
          .post("/petugas", form, conf)
          .then((response) => {
            this.message = "Mohon tunggu...";
            if (this.search == "") {
              this.getData();
            } else {
              this.searching();
            }
            this.message = "Berhasil di tambahkan!";
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        let form = {
          nama: this.nama,
          username: this.username,
          password: this.password,
          level: this.level,
          telp: this.telp,
        };
        this.axios
          .put("/petugas/" + this.id, form, conf)
          .then((response) => {
            this.message = "Mohon tunggu...";
            if (this.search == "") {
              this.getData();
            } else {
              this.searching();
            }
            this.message = "Berhasil di ubah!";
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    Drop: function (id) {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      if (confirm("Apakah anda yakin ingin menghapus data ini?")) {
        this.message = "Mohon tunggu...";
        this.axios
          .delete("/petugas/" + id, conf)
          .then((response) => {
            this.getData();
            this.message = "Berhasil di hapus!";
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
  },
  mounted() {
    this.token = localStorage.getItem("Authorization");
    this.getData();
  },
};
</script>
