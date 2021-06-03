<template>
  <div>
    <div class="row">
      <div class="col">
        <div class="card">
          <h3
            class="card-header d-flex justify-content-between align-items-center"
          >
            Laporan
            <!-- <button class="btn btn-icon btn-success" type="button" v-b-modal.modalpengaduan v-on:click="Add">
                            <span class="btn-inner--icon"><i class="fa fa-plus"></i></span>
                            <span class="btn-inner--text">Tambah</span>
                        </button> -->
          </h3>

          <!-- Light table -->
          <div>
                              <vue-html2pdf
                                :show-layout="false"
                                :float-layout="true"
                                :enable-download="false"
                                :preview-modal="true"
                                :paginate-elements-by-height="5000"
                                filename="Invoice"
                                :pdf-quality="2"
                                :manual-pagination="false"
                                  pdf-format="a4"
                                  pdf-orientation="portrait"
                                  pdf-content-width="800px"
              
                      @hasStartedGeneration="hasStartedGeneration()"
                      @hasGenerated="hasGenerated($event)"
                      ref="html2Pdf" >
                      <section slot="pdf-content">
                          <div class="invoice-box">
                      <table>
                        <tr class="top">
                          <td colspan="2">
                            <table>
                              <tr>
                              <td class="title">
                                <img src="../../public/assets/img/brand/logo.png" alt="Company logo" width="100px" />
                              </td>
                              <td>
                                Dengan: <b>{{reports.nama}}</b> <br />
                                <!-- Tanggal: <b>{{new Date().toLocaleString()}}</b> <br> -->
                                <!-- Tanggal: <b>{{reportp.tgl_pengaduan}}</b> <br> -->
                                <!-- Tanggal: <span v-if="reportt.tgl_tanggapan">{{reportt.tgl_tanggapan}}</span> <span v-else>"Belum di tanggapi"</span> <br /> -->
                                Kategori: <b>{{reportk.nama_kategori}}</b> <br />
                                Status: <b class="text-success" v-if="reportp.status === 'selesai'">{{reportp.status}}</b>
                                <b class="text-danger" v-if="reportp.status === 'tolak'">{{reportp.status}}</b>
                                <b class="text-warning" v-if="reportp.status === 'proses'">{{reportp.status}}</b>
                                <b class="text-info" v-if="reportp.status === 'terkirim'">{{reportp.status}}</b>
                              </td>
                            </tr>
                          </table>
                        </td>
                      </tr>
  
                      <tr class="heading">
                        <td>Data User</td>
                        <td> #</td>
                      </tr>

                       <tr class="details">
                        <td>NIK</td>
                        <td> {{reports.nik}} </td>
                      </tr>

                      <tr class="details" :items="user">
                        <td>Nama Lengkap</td>
                        <td> {{reports.nama}} </td>
                      </tr>

                      <tr class="details" :items="user">
                        <td>Username</td>
                        <td> {{reports.username}} </td>
                      </tr>

                       <tr class="details" :items="user">
                        <td>Telepon</td>
                        <td> {{reports.telp}} </td>
                      </tr>

                      <tr class="heading">
                        <td>Data Pengaduan</td>

                        <td> #</td>
                      </tr>

                      <tr class="item">
                        <td>Tanggal Pengaduan</td>
                        <td> {{reportp.tgl_pengaduan}} </td>
                      </tr>

                      <tr class="item">
                        <td>Laporan</td>
                        <td> {{reportp.isi_laporan}} </td>
                      </tr>

                      <tr class="item">
                        <td>Status</td>
                        <td> <span v-if="reportp.status"> {{reportp.status}} </span> <span v-else>-</span></td>
                      </tr>
                       <!-- <tr class="item last">
                        <td>Bukti</td>
                        <td> <img v-if="reportp.foto" 
                              style="width: 50px;
                              height: 50px;
                              border-radius: 10%;" 
                              :src="'http://localhost:8000/uploads/' + reportp.foto">  
                        </td>
                      </tr> -->

                    </table>
                  </div>
                      </section>
                  </vue-html2pdf>
                </div>
          <div class="table-responsive">
              <!-- <b-nav-form> -->
                  <b-form-input size="sm" placeholder="Tgl Pengaduan..." v-model="search" v-on:keyup.enter="Cari"></b-form-input>
                  <!-- <b-button size="sm" class="my-2 my-sm-0" type="submit" variant="success" v-on:click="Cari(data.item.id)">Search</b-button> -->
                <!-- </b-nav-form> -->
            <b-table
              striped
              hover
              :items="report"
              :fields="fields"
              class="table align-items-center table-flush"
            >
              <template v-slot:cell(nama_kategori)="data">
                <b-badge variant="default">{{
                  data.item.nama_kategori
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
              <!-- <template v-slot:cell(foto)="data">
                <b-badge
                  class="btn"
                  variant="secondary"
                  v-on:click="getFoto(data.item.id_pengaduan)"
                  >Lihat Foto</b-badge
                >
              </template> -->
              <template v-slot:cell(Aksi)="data">
                <!-- <b-button
                  size="sm"
                  v-on:click="Lihat(data.item.id_pengaduan)"
                  v-b-modal.modallihat
                  class="btn btn-outline-info"
                  >Lihat</b-button> -->
                  <b-button
                  size="sm"
                  v-on:click="generateReport(data.item.id_pengaduan)"
                  class="btn btn-outline-info"
                  >Download PDF</b-button>
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

        <!-- <div>
          <label for="foto_detail" class="col-form-label">Foto</label>
          <div id="foto_detail">
            <img width="100%" :src="'http://localhost:8000/uploads/' + foto" />
          </div>
        </div> -->
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
import VueHtml2pdf from 'vue-html2pdf'

export default {
  data: function () {
    return {
      search: "",
      reportp: "",
      reportk: "",
      reportt: "",
      reports: "",
      id_pengaduan: "",
      isi_laporan: "",
      tgl_pengaduan: "",
      nama: "",
      tahun: "",
      id_user: "",
      id_kategori: "",
      nama_kategori: "",
      status: "",
      tanggapan: "",
      foto: "",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      token: "",
      user: [],
      pengaduan: [],
      report: [],
      detailReport: [],
      kategori: [],
      fields: [
        "Aksi",
        "isi_laporan",
        "nama",
        "nama_kategori",
        "status",
        "tgl_pengaduan",
      ],
      fields_detail: [
        "Aksi",
        "tgl_pengaduan",
        "isi_laporan",
        "kategori",
        "foto",
        "status",
      ],
    };
  },

  components: {
        VueHtml2pdf
  },

  methods: {
    getData: function () {
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.message = "Mohon tunggu...";
      this.axios.get("/pengaduan/report/"+this.perPage + "/" + offset, conf)
      .then(response => {
        if(response.data.status == 1){
        //   this.$bvToast.hide("loadingToast");
        this.message = "";
          this.report = response.data.report;
          this.rows = response.data.count;
        } else {
          this.$bvToast.hide("loadingToast");
          this.message = "Gagal menampilkan data pelanggaran."
          this.$bvToast.show("message");
          this.$router.push({name: "laporan"})
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

    Cari : function(id){
      let conf = { headers: { Authorization: "Bearer " + this.token } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      let form = new FormData();
      form.append("find", this.search)
      this.axios.post("/pengaduan/report/" +this.perPage + "/" + offset,form,conf)
      .then(response => {
        if(response.data.status == 1){
          this.$bvToast.hide("loadingToast");
          this.report = response.data.report;
          this.rows = response.data.count;
        }else {
          this.$bvToast.hide("loadingToast");
          this.message = "Gagal menampilkan data detail poin."
          this.$bvToast.show("message");
        }
      })
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
        .get("/pengaduan/report/" + id, conf)
        .then((response) => {
          if (response.data.success == true) {
            this.action = "update";
            // this.id_pengaduan = response.data.data.report[0].id_pengaduan;
            this.isi_laporan = response.data.data.report[0].isi_laporan;
            this.tgl_pengaduan = response.data.data.report[0].tgl_pengaduan;
            // this.foto = response.data.data.report[0].foto;
            this.status = response.data.data.report[0].status;
            this.id_kategori = response.data.data.report[0].nama_kategori;
            this.nama = response.data.data.report[0].nama;
          } else {
            this.message = "Gagal menampilkan data pengaduan.";
            this.$router.push({ name: "laporan" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    generateReport (id_pengaduan) {
       let conf = { headers: { Authorization: "Bearer " + this.token } };
      //  console.log(id);
       this.$bvToast.show("loadingToast");
       this.axios.get("/pengaduan/" + id_pengaduan, conf)
       .then(response => {
        //  console.log(response);
          if(response.data.success == true){
          this.$bvToast.hide("loadingToast");
          // this.pengaduan = response.data.data.pengaduan;
          this.reportp = response.data.data.pengaduan[0];
          this.reports = response.data.data.pengaduan[0].user;
          this.reportt = response.data.data.pengaduan[0].tanggapan;
          this.reportk = response.data.data.pengaduan[0].kategori;
          this.$refs.html2Pdf.generatePdf()
          } else {
          this.$bvToast.hide("loadingToast");
          this.message = "Gagal menampilkan data pengaduan."
          this.$bvToast.show("message");
          this.$router.push({name: "laporan"})
        }
      })
      .catch(error => {
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
<style scoped>
body {
				font-family: 'Helvetica Neue', 'Helvetica', Helvetica, Arial, sans-serif;
				text-align: center;
				color: #777;
			}

			body h1 {
				font-weight: 300;
				margin-bottom: 0px;
				padding-bottom: 0px;
				color: #000;
			}

			body h3 {
				font-weight: 300;
				margin-top: 10px;
				margin-bottom: 20px;
				font-style: italic;
				color: #555;
			}

			body a {
				color: #06f;
			}

			.invoice-box {
				max-width: 800px;
				margin: auto;
				padding: 30px;
				border: 1px solid #eee;
				box-shadow: 0 0 10px rgba(0, 0, 0, 0.15);
				font-size: 16px;
				line-height: 24px;
				font-family: 'Helvetica Neue', 'Helvetica', Helvetica, Arial, sans-serif;
				color: #555;
			}

			.invoice-box table {
				width: 100%;
				line-height: inherit;
				text-align: left;
				border-collapse: collapse;
			}

			.invoice-box table td {
				padding: 5px;
				vertical-align: top;
			}

			.invoice-box table tr td:nth-child(2) {
				text-align: right;
			}

			.invoice-box table tr.top table td {
				padding-bottom: 20px;
			}

			.invoice-box table tr.top table td.title {
				font-size: 45px;
				line-height: 45px;
				color: #333;
			}

			.invoice-box table tr.information table td {
				padding-bottom: 40px;
			}

			.invoice-box table tr.heading td {
				background: #eee;
				border-bottom: 1px solid #ddd;
				font-weight: bold;
			}

			.invoice-box table tr.details td {
				padding-bottom: 20px;
			}

			.invoice-box table tr.item td {
				border-bottom: 1px solid #eee;
			}

			.invoice-box table tr.item.last td {
				border-bottom: none;
			}

			.invoice-box table tr.total td:nth-child(2) {
				border-top: 2px solid #eee;
				font-weight: bold;
			}

			@media only screen and (max-width: 600px) {
				.invoice-box table tr.top table td {
					width: 100%;
					display: block;
					text-align: center;
				}

				.invoice-box table tr.information table td {
					width: 100%;
					display: block;
					text-align: center;
				}
			}
</style>