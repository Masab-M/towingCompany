<template>
  <b-container>
    <b-row>
      <b-col>
        <div class="towingFHead">
          <div class="towingHeading">
            <h2>
              Towing Service
            </h2>
          </div>
          <div class="headDesc">
            <p>Dispatch FORM</p>
          </div>
        </div>
        <b-row>
          <b-col xs="6">
            <div class="jobtowing details">
              <div class="singledetail">
                <b-row>
                  <b-col>
                    <span>
                      Make
                    </span>
                  </b-col>
                  <b-col>
                    <span>
                      {{ ticketData.job.make }}
                    </span>
                  </b-col>
                </b-row>
              </div>
              <div class="singledetail">
                <b-row>
                  <b-col>
                    <span>
                      Model
                    </span>
                  </b-col>
                  <b-col>
                    <span>
                      {{ ticketData.job.model }}
                    </span>
                  </b-col>
                </b-row>
              </div>
              <div class="singledetail">
                <b-row>
                  <b-col>
                    <span>
                      Year
                    </span>
                  </b-col>
                  <b-col>
                    <span>
                      {{ ticketData.job.year }}
                    </span>
                  </b-col>
                </b-row>
              </div>
              <div class="singledetail">
                <b-row>
                  <b-col>
                    <span>
                      Vin No
                    </span>
                  </b-col>
                  <b-col>
                    <span>
                      {{ ticketData.job.vinNo }}
                    </span>
                  </b-col>
                </b-row>
              </div>
            </div>
          </b-col>
        </b-row>
        <form action="" class="creditForm" @submit.prevent="handleSubmit">
          <b-row>
            <b-col>
              <b-form-group id="input-group-1" label="Upload Picture" label-for="input-ca">
                <input type="file" id="imageUpload" accept="image/*" @change="handleImageUpload" multiple>
                <b-form-text id="password-help-block">
                  Upload More Images up to {{ fileNumber }}
                </b-form-text>
              </b-form-group>
            </b-col>
            <b-col>
              <div v-if="files.length > 0" class="selectedDImages">
                <h3>Selected Images</h3>
                <b-row>
                  <b-col xs="3" class="image-container" v-for="(file, index) in files" :key="index">
                    <img :src="getFileUrl(file)" class="image">
                    <p class="caption">{{ file.name }}</p>
                  </b-col>
                </b-row>
                </div>
            </b-col>
          </b-row>
          <b-row>
            <b-col>
              <div class="submitBtnForm">
                <button type="submit">Submit</button>
              </div>
            </b-col>
          </b-row>
        </form>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import axios from 'axios';
import router from '../../router';

export default {
  mounted() {
    this.getjobDetails()
  },
  data() {
    return {
      files: [],
      fileNumber:10,
      id: this.$route.query.ticket,
      ticketData: {
        job: {}
      }
    }
  },
  methods: {
    handleImageUpload(event) {
      const images = event.target.files;
      console.log(images.length+this.files.length);
      if (images.length+this.files.length > 10) {
        // alert the user that the limit has been exceeded
        alert('You can select up to 10 images only!');
        // clear the selected files
        event.target.value = '';
      }
      this.files = [...this.files,...images]
      this.fileNumber=this.fileNumber-this.files.length
    },
    handleSubmit() {
      if(this.files.length===0) return alert('Atleast Select One Image')
      const formData = new FormData();
      for (let i = 0; i < this.files.length; i++) {
        let file = this.files[i];
        formData.append("files", file);
      }
      formData.append('ticket', this.ticketData.ticketNumber);
      formData.append('job', this.ticketData.job.id);
      formData.append('id', this.ticketData.id);
      console.log(this.files);
      axios.post(`${import.meta.env.VITE_LIVE}/dispatchMail`, formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }).then((result) => {
        alert('send')
        router.push('/')
      }).catch((err) => {
        alert('not Send')
      });
    },
    getjobDetails() {
      axios.get(`${import.meta.env.VITE_LIVE}/ticket?ticketNumber=${this.id}`).then((res) => {
        console.log(res);
        this.ticketData = res.data
      }).catch((err) => {
        alert('ticket is expired');
        router.push('/')
      })
    },
    getFileUrl(file) {
      return URL.createObjectURL(file);
    },
  }
}
</script>

<style scoped>
.selectedDImages img {
  width: 100px;
}
.text-muted {
  display: block;
}
</style>