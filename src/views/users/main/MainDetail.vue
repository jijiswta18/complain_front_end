<template>
  <div class="page-main">
      <div class="head-topic">
          <v-container>
              <v-row>  
                <span class="head-left">ประชาสัมพันธ์</span>
              </v-row>
          </v-container>
      </div>
      <div class="section-main">
          <v-container >
            <!-- ประชาสัมพันธ์ -->     
            <div class="text-left">
              <breadcrumbsView :items="item"/>
              <v-card class="text-left box-shadow">

                <v-card-title><div>{{ data.announce_title }}</div></v-card-title>
                <v-card-subtitle>
                    <span>ข่าวประชาสัมพันธ์</span>
                    
                    &nbsp;<span><i class="fas fa-calendar-alt"></i> {{data.start_date}}</span>
                    &nbsp;<span><i class="far fa-eye"></i> {{data.number_preview}}</span>
                </v-card-subtitle>
                <v-card-text class="px-4 py-0">
                    <div v-html="data.announce_content"></div>
            
                </v-card-text>
                <v-card-actions class="text-left">
                    <v-btn  class="text-none btn-bg-blue" variant="outlined" @click="showFile(data.file_type, data.file_name, data.file_original)">ดาวน์โหลกเอกสาร</v-btn>
                </v-card-actions>
              </v-card>
            </div>
          </v-container>
      </div>
     <v-overlay class="style-bg" :opacity="opacity" :absolute="absolute"  :value="overlayImg">
      <img :src="url" />
      <v-btn
          class="btn-overlay"
          icon
          @click="overlayImg = false"
      >
      <v-icon dark>fa-xmark</v-icon>
      </v-btn>
    </v-overlay>
  </div>
</template>

<script>
import axios from "axios";
import moment from 'moment';
import breadcrumbsView from '@/components/breadcrumbsView.vue';
export default {
  components: { breadcrumbsView },
  data: () => ({
      data: {},
      url: '',
      opacity: 1,
      absolute: false,
      overlayImg: false,
      item: [
        {
          text: 'หน้าหลัก',
          disabled: false,
          href: '/',
        },
        {
          text: 'ประชาสัมพันธ์',
          disabled: true,
          href: 'breadcrumbs_link_1',
        },
        
    ],
  }),
  mounted() {
      this.getDataDetailAnnounce();
      console.log(this.$route.params.id);
  },
  methods: {
    showFile(file_type, file_name, file_original){
      if(file_type == 'application/pdf'){
        this.urlPdfFiles('UrlFilesAnnounce', file_name, file_original)
      }else{
        this.urlFiles('UrlFilesAnnounce', file_name, file_original)
      }
    },
      async urlPdfFiles(url,file_name, file_original){
      axios({
          url: `/api/get/user/pdf/${url}`,
          params: {"filename":file_name},
          method: 'GET',
          responseType: 'blob',
      }).then((response) => {
            var fileURL = window.URL.createObjectURL(new Blob([response.data], { type: 'application/pdf' }));
            var fileLink = document.createElement('a');

            fileLink.href = fileURL;
            fileLink.setAttribute('download', file_original);
            document.body.appendChild(fileLink);

            window.open(fileLink, "_blank");

      });
    },
    async urlFiles(url,file_name, file_original){
     
      let path = await `/api/get/user/${url}?filename=${file_name}`
      
      let res = await axios.get(`${path}`)

      console.log();

      this.url = await res.data

      const downloadLink = document.createElement("a");
      downloadLink.href = res.data;
      downloadLink.download = file_original;
      downloadLink.click();

      this.overlayImg = await !this.overlayImg 
    },
      async getDataDetailAnnounce() {
          let path = await `/api/user/get/announceDetail`;
          let response = await axios.get(`${path}/` + this.$route.params.id);
          this.data = await response.data.data[0];
          this.data.start_date = await moment(response.data.data[0].start_date).add(543, 'year').tz("Asia/Bangkok").locale('th').format('DD MMMM YYYY');
          // await setTimeout(() => (this.$refs.loader.overlay = false), 300);
      },
  },

}
</script>

<style>
  .box-shadow{
    box-shadow: none!important;
  }
  .btn-bg-blue{
    background: #167dc2!important;
    color: white!important;
  }
</style>