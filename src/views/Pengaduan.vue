<template>
  <div>
    <div class="row">
      <div class="col">
        <div class="card">
          <h3
            class="card-header d-flex justify-content-between align-items-center"
          >
            Data Pengaduan
            <!-- <button class="btn btn-icon btn-success" type="button" v-b-modal.modalpengaduan v-on:click="Add">
                            <span class="btn-inner--icon"><i class="fa fa-plus"></i></span>
                            <span class="btn-inner--text">Tambah</span>
                        </button> -->
          </h3>

          <!-- Light table -->
          <div class="table-responsive">
            <b-table
              striped
              hover
              :items="pengaduan"
              :fields="fields"
              class="table align-items-center table-flush"
            >
              <template v-slot:cell(kategori)="data">
                <b-badge variant="default">{{
                  data.item.kategori.nama_kategori
                }}</b-badge>
              </template>
              <template v-slot:cell(status)="data">
                <b-badge
                  variant="primary"
                  v-if="data.item.status === 'terkirim'"
                  >{{ data.item.status }}</b-badge
                >
                <b-badge
                  variant="warning"
                  v-if="data.item.status === 'proses'"
                  >{{ data.item.status }}</b-badge
                >
                <b-badge
                  variant="success"
                  v-if="data.item.status === 'selesai'"
                  >{{ data.item.status }}</b-badge
                >
                <b-badge variant="danger" v-if="data.item.status === 'tolak'">{{
                  data.item.status
                }}</b-badge>
              </template>
              <template v-slot:cell(foto)="data">
                <b-badge
                  class="btn"
                  variant="secondary"
                  v-on:click="getFoto(data.item.id_pengaduan)"
                  >Lihat Foto</b-badge
                >
              </template>
              <template v-slot:cell(Aksi)="data">
                <b-button
                  size="sm"
                  v-on:click="Lihat(data.item.id_pengaduan)"
                  v-b-modal.modallihat
                  class="btn btn-outline-info"
                  >Lihat</b-button
                >&nbsp;
                <b-button
                  size="sm"
                  v-on:click="Edit(data.item.id_pengaduan)"
                  v-b-modal.modaledit
                  class="btn btn-outline-warning"
                  >Ubah Status</b-button
                >&nbsp;
                <b-button
                  size="sm"
                  v-if="data.item.status === 'tolak'"
                  v-b-modal.modalTanggapi
                  v-on:click="Tanggapi(data.item.id_pengaduan)"
                  class="btn btn-success"
                  >Tanggapi</b-button
                >
              </template>
            </b-table>

            <!-- <div class="text-center my-3">
              <small class="text-red">{{ message }}</small>
            </div> -->
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
    <b-modal id="modalpengaduan" size="md" @ok="Save">
      <template v-slot:modal-title> Form Data Pengaduan </template>
      <form ref="form" enctype="multipart/form-data">
        <div class="form-group">
          <label for="tgl_pengaduan" class="form-control-label"
            >Tanggal Pengaduan</label
          >
          <input
            type="date"
            v-model="tgl_pengaduan"
            name="tgl_pengaduan"
            class="form-control"
            id="tgl_pengaduan"
            placeholder="Tanggal Pengaduan"
          />
        </div>
        <div class="form-group">
          <label for="isi_laporan" class="form-control-label"
            >Isi Laporan</label
          >
          <input
            type="text"
            v-model="isi_laporan"
            name="isi_laporan"
            class="form-control"
            id="isi_laporan"
            placeholder="Isi Laporan"
          />
        </div>
        <div class="form-group">
          <label for="id_kategori" class="col-form-label">Kategori</label>
          <b-form-select
            id="id_kategori"
            v-model="id_kategori"
            :options="kategori"
          ></b-form-select>
        </div>
        <div class="form-group">
          <label for="foto" class="form-control-label">Foto</label>
          <input
            type="file"
            id="foto"
            class="form-control"
            @change="uploadImage($event)"
            placeholder="Foto"
          />
        </div>
      </form>
    </b-modal>

    <b-modal id="modalFoto" size="md" hide-footer>
      <template v-slot:modal-title> Detail Foto </template>
      <div id="foto_detail">
        <img width="100%" :src="'http://localhost:8000/uploads/' + foto" />
      </div>
      <b-button
        class="mb-3 mt-3 bg-dark text-white"
        block
        @click="$bvModal.hide('modalFoto')"
        >Close</b-button
      >
    </b-modal>

    <b-modal id="modaledit" size="sm" @ok="Save">
      <template v-slot:modal-title>
        Ubah Status Laporan
        <b-badge variant="info" v-if="status === 'terkirim'">{{
          status
        }}</b-badge>
        <b-badge variant="warning" v-if="status === 'proses'">{{
          status
        }}</b-badge>
        <b-badge variant="success" v-if="status === 'selesai'">{{
          status
        }}</b-badge>
        <b-badge variant="danger" v-if="status === 'tolak'">{{
          status
        }}</b-badge>
      </template>
      <div class="form-group">
        <label for="status" class="col-form-label">Status</label>
        <select class="form-control" name="status" id="status" v-model="status">
          <!-- <option value="" checked></option> -->
          <option value="proses">Proses</option>
          <option value="selesai">Selesai</option>
          <option value="tolak">Tolak</option>
        </select>
      </div>
    </b-modal>

    <b-modal id="modalTanggapi" size="sm" @ok="Save">
      <template v-slot:modal-title> Tanggapi Laporan </template>
      <div class="form-group">
        <label for="status" class="col-form-label">Tanggapan</label>
        <textarea
          v-model="tanggapan"
          class="form-control"
          id="tanggapan"
        ></textarea>
      </div>
    </b-modal>

    <b-modal id="modallihat" size="md" hide-footer>
      <template v-slot:modal-title>
        Detail Pengaduan
        <b-badge variant="info" v-if="status === 'terkirim'">{{
          status
        }}</b-badge>
        <b-badge variant="warning" v-if="status === 'proses'">{{
          status
        }}</b-badge>
        <b-badge variant="success" v-if="status === 'selesai'">{{
          status
        }}</b-badge>
        <b-badge variant="danger" v-if="status === 'tolak'">{{
          status
        }}</b-badge>
      </template>
      <form ref="form">
        <div class="form-group">
          <label for="tgl_pengaduan_detail" class="form-control-label"
            >Tanggal Pengaduan</label
          >
          <input
            type="date"
            readonly
            v-model="tgl_pengaduan"
            name="tgl_pengaduan"
            class="form-control"
            id="tgl_pengaduan_detail"
          />
        </div>
        <div class="form-group">
          <label for="isi_laporan_detail" class="form-control-label"
            >Isi Laporan</label
          >
          <textarea
            readonly
            v-model="isi_laporan"
            class="form-control"
            id="isi_laporan_detail"
          ></textarea>
        </div>
        <div class="form-group">
          <label for="id_kategori_detail" class="col-form-label"
            >Kategori</label
          >
          <b-form-select
            disabled
            id="id_kategori_detail"
            v-model="id_kategori"
            :options="kategori"
          ></b-form-select>
        </div>

        <div>
          <label for="foto_detail" class="col-form-label">Foto</label>
          <div id="foto_detail">
            <img width="100%" :src="'http://localhost:8000/uploads/' + foto" />
          </div>
        </div>
      </form>

      <b-button
        class="mb-3 mt-3 bg-dark text-white"
        block
        @click="$bvModal.hide('modallihat')"
        >Close</b-button
      >
    </b-modal>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      search: "",
      id_pengaduan: "",
      isi_laporan: "",
      tgl_pengaduan: "",
      id_kategori: "",
      status: "",
      tanggapan: "",
      foto: "",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      token: "",
      pengaduan: [],
      kategori: [],
      fields: [
        "Aksi",
        "tgl_pengaduan",
        "isi_laporan",
        "kategori",
        "foto",
        "status",
      ],
    };
  },

  methods: {
    getData: function () {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.message = "Mohon tunggu...";
      this.axios
        .get("/pengaduan/" + this.perPage + "/" + offset, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.message = "";
            this.pengaduan = response.data.data.pengaduan;
            this.rows = response.data.data.count;
          } else {
            this.message = "Gagal menampilkan data pengaduan.";
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

    getKategoriDropdown: function () {
      //ambil data kategori untuk dropdown
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      this.axios.get("/kategori", conf).then((response) => {
        let list_kategori = [];
        let json_kategori = response.data.data.kategori;
        json_kategori.forEach((element) => {
          list_kategori.push({
            value: element.id_kategori,
            text: element.nama_kategori,
          });
        });
        this.kategori = list_kategori;
      });
    },

    uploadImage(e) {
      this.foto = e.target.files[0];
    },

    Add: function () {
      this.action = "insert";
      this.isi_laporan = "";
      this.tgl_pengaduan = "";
      this.id_kategori = "";
      this.foto = "";
      this.status = "";
      this.getKategoriDropdown();
    },

    Edit: function (id) {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      this.axios
        .get("/pengaduan/" + id, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.action = "update";
            this.id_pengaduan = response.data.data.pengaduan[0].id_pengaduan;
            this.status = response.data.data.pengaduan[0].status;
          } else {
            this.message = "Gagal menampilkan data pengaduan.";
            this.$router.push({ name: "pengaduan" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    Tanggapi: function (id) {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      this.axios
        .get("/pengaduan/" + id, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.action = "tanggapi";
            this.id_pengaduan = response.data.data.pengaduan[0].id_pengaduan;
            this.status = response.data.data.pengaduan[0].status;
          } else {
            this.message = "Gagal menampilkan data pengaduan.";
            this.$router.push({ name: "pengaduan" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    getFoto: function (id) {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      this.axios
        .get("/pengaduan/" + id, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.foto = response.data.data.pengaduan[0].foto;
            this.$bvModal.show("modalFoto");
          } else {
            this.message = "Gagal menampilkan data foto pengaduan.";
            this.$router.push({ name: "pengaduan" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    Lihat: function (id) {
      this.getKategoriDropdown();

      let conf = {
        headers: {
          Authorization: "Bearer " + this.token,
          "content-type": "multipart/form-data",
        },
      };
      this.axios
        .get("/pengaduan/" + id, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.action = "update";
            this.id_kategori = response.data.data.pengaduan[0].id_kategori;
            this.isi_laporan = response.data.data.pengaduan[0].isi_laporan;
            this.tgl_pengaduan = response.data.data.pengaduan[0].tgl_pengaduan;
            this.foto = response.data.data.pengaduan[0].foto;
            this.status = response.data.data.pengaduan[0].status;
          } else {
            this.message = "Gagal menampilkan data pengaduan.";
            this.$router.push({ name: "pengaduan" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    Save: function () {
      let conf = {
        headers: {
          Authorization: "Bearer " + this.token,
          "content-type": "multipart/form-data",
        },
      };
      if (this.action === "insert") {
        let form = new FormData();
        form.append("id_kategori", this.id_kategori);
        form.append("isi_laporan", this.isi_laporan);
        form.append("tgl_pengaduan", this.tgl_pengaduan);
        form.append("foto", this.foto);

        this.axios
          .post("/pengaduan", form, conf)
          .then((response) => {
            this.message = "Mohon tunggu...";
            if (this.search == "") {
              this.getData();
            } else {
              this.searching();
            }
            this.message = "";
          })
          .catch((error) => {
            console.log(error);
          });
      } else if (this.action === "update") {
        let form = new FormData();
        form.append("id_pengaduan", this.id_pengaduan);
        form.append("status", this.status);
        this.axios
          .post("/pengaduan/status", form, conf)
          .then((response) => {
            this.message = "Mohon tunggu...";
            if (this.search == "") {
              this.getData();
            } else {
              this.searching();
            }
            this.message = "Status berhasil di ubah!";
          })
          .catch((error) => {
            console.log(error);
          });
      } else if (this.action === "tanggapi"){
        let form = new FormData();
        form.append("id_pengaduan", this.id_pengaduan);
        form.append("tanggapan", this.tanggapan);
        this.axios
          .post("/pengaduan/tanggapan", form, conf)
          .then((response) => {
            this.message = "Mohon tunggu...";
            if (this.search == "") {
              this.getData();
            } else {
              this.searching();
            }
            this.message = "Laporan berhasil di tanggapi!";
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
          .delete("/pengaduan/" + id, conf)
          .then((response) => {
            this.getData();
            this.message = "";
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
