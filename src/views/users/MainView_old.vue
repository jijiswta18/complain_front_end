<template>
    <div class="page-main">
      
        <div class="head-topic">
            <v-container>
                <v-row>
                    <span class="head-left">แบบฟอร์มลงทะเบียนเรื่องร้องเรียน</span>
                </v-row>
            </v-container>
        </div>
        <div class="section-main">
            <v-container class="mt-3">
                <span class="h1">ขั้นตอนการร้องเรียนเรื่องทุจริต</span>
                    <v-stepper v-model="e1" class="step">
                        <v-stepper-header class="stepper-header mt-3">
                                <v-stepper-step :complete="e1 > 1" step="1" >ข้อตกลงเเละเงื่อนไขการร้องเรียน</v-stepper-step>
                            <v-divider></v-divider>

                            <v-stepper-step :complete="e1 > 2" step="2">ข้อมูลผู้ร้องเรียน</v-stepper-step>

                            <v-divider></v-divider>

                            <v-stepper-step :complete="e1 > 3" step="3">ข้อมูลการร้องเรียน</v-stepper-step>

                            <v-divider></v-divider>

                            <v-stepper-step step="4" :complete="isOkay" >บันทึกรายการ</v-stepper-step>
                        </v-stepper-header>
                        <v-stepper-items>
                        
                            <v-stepper-content step="1">
                                <stepOne/>
                                <div class="text-center">
                                    <v-btn color="#003366" class="text-white" @click="checkStepOne">  ข้าพเจ้ายอมรับข้อตกลงและเงื่อนไข</v-btn>
                                </div>
                            </v-stepper-content>
                            <v-stepper-content step="2">
                                <v-form
                                    ref="formRegister"
                                    v-model="validOne"
                                    lazy-validation
                                >
                                    <StepTwo ref="user"/>

                                    <div class="text-right">
                                        <v-btn  class="btn-back" @click="e1 = 1">ย้อนกลับ</v-btn>
                                        <v-btn color="#003366" class="btn-next text-white"  @click="checkStepTwo">ถัดไป</v-btn>
                                    </div>
                                </v-form>
                            </v-stepper-content>
                            <v-stepper-content step="3">
                                <v-form
                                    ref="formOfficer"
                                    v-model="validOne"
                                    lazy-validation
                                >
                                    <StepTree ref="employee"/>
                                    <div class="text-right">
                                        <v-btn class="btn-back"  @click="e1 = 2">ย้อนกลับ</v-btn>
                                        <v-btn color="#003366" class="btn-next text-white" @click="checkStepTree">ถัดไป</v-btn>
                                    </div>
                                </v-form>
                            </v-stepper-content>
                            <v-stepper-content step="4">
                                <v-form
                                    ref="formSubmit"
                                    v-model="validOne"
                                    lazy-validation
                                >
                                <v-row justify="space-between">
                                    <v-col v-if="user.item" cols="12" md="6" class="text-left">
                                        <div class="h2 mb-2">ข้อมูลผู้ร้อง / ข้อมูลติดต่อ</div>
                                        <p>อีเมล : {{ user.item.email }}</p>
                                        <p>ชื่อ-สกุล : {{ user.item.name }} {{ user.item.lastname }}</p>
                                        <p>เพศ : {{ user.item.gender }}</p>
                                        <p>อายุ : {{ user.item.age }} ปี</p>
                                        <p>โทรศัพท์มือถือ : {{ user.item.phone }}</p>
                                        <p>เบอร์ติดต่ออื่น ๆ : {{ user.item.phone_other }}</p>
                                        <p>ที่อยู่ : {{ user.item.address }}</p>
                                    </v-col>
                                    <v-col v-if="employee" cols="12" md="6" class="text-left">
                                        <div class="h2 mb-2">เจ้าหน้าที่ / หน่วยงาน </div>
                                        <p>ชื่อ-สกุล : {{ employee.name }} {{ employee.lastname }}</p>
                                        <p>หน่วยงาน : {{ employee.division }}</p>
                                        <p>รูปพรรณสันฐาน : {{ employee.description_face }}</p>
                                        <p>เรื่องร้องเรียน : {{ employee.complain_topic }}</p>
                                        <p>สถานที่เกิดเหตุ : {{ employee.complain_location }}</p>
                                        <p>ช่วงวัน - เวลาเกิดเหตุ : ตั้งแต่ : {{ employee.complain_start_date }} - {{ employee.complain_end_date }}</p>
                                        <p>รายละเอียดเรื่องร้องเรียน : {{ employee.complain_detail }}</p>
                                        <p>เอกสารประกอบ : </p>
                                        <div v-for="(item, i) in employee.files" :key="i" @click="dataUrl(item)">{{  item.name }}</div>
                                    </v-col>
                                </v-row>
                                    
                                    <div class="text-right">
                                        <v-btn class="btn-back"  @click="e1 = 3">ย้อนกลับ</v-btn>
                                        <v-btn color="#003366" class="btn-next text-white" @click="submit">บันทึก</v-btn>
                                    </div>
                                </v-form>
                            </v-stepper-content>

                        </v-stepper-items>
                    </v-stepper>

            </v-container>
        </div>
    </div>
  

</template>
  
<script>
import axios  from 'axios'
import moment from 'moment'
import Swal from 'sweetalert2'
import stepOne from '@/components/step/stepOne.vue'
import StepTwo from '@/components/step/stepTwo.vue'
import StepTree from '@/components/step/stepTree.vue'
  export default {
  components: { stepOne, StepTwo, StepTree },
    data: () => ({
        e1: 1,
        user: {},
        employee: {},
        isOkay: false,
        step_one : false,
        validOne: true,
        validTwo: true,
        blobUrl: null,
        dataString: "Hello, world!"
    }),
    methods: {
        checkStepOne(){
            this.e1 = 2
            this.step_one = true

        },
        async checkStepTwo(){
            if(this.$refs.formRegister.validate()){

                this.user = this.$refs.user

                try {
                    
                    let fd_mail = await { "email": this.user.item.email}
                    
                    let path = await `/api/user/checkMail`
                    let response = await axios.post(`${path}`, fd_mail)

                    if(response.data.status == 200){
                        await Swal.fire({
                            icon: 'error',
                            title: 'Oops...',
                            text: 'มีอีเมลนี่ในระบบเเล้ว',
                            // footer: '<a href="">Why do I have this issue?</a>'
                        })
                    }
                
                } catch (error) {

                    this.e1 = await 3
                }
            }
        },
        checkStepTree(){
            if(this.$refs.formOfficer.validate()){
                this.e1 = 4
                this.employee = this.$refs.employee
            }
        },
        dataUrl(v){
            const blobUrl = window.URL.createObjectURL(v);
            window.open(blobUrl, '_blank');
        },
        async submit(){
           try {
                let fd = await {
                    "email"                 : this.user.item.email,
                    "name"                  : this.user.item.name,
                    "lastname"              : this.user.item.lastname,
                    "gender"                : this.user.item.gender,
                    "age"                   : this.user.item.age,
                    "phone"                 : this.user.item.phone,
                    "phone_other"           : this.user.item.phone_other,
                    "address"               : this.user.item.address,     
                    "province"              : this.user.province !== null ? this.user.province.id || this.user.province : null,
                    "district"              : this.user.district !== null ? this.user.district.id || this.user.district : null,
                    "subdistrict"           : this.user.subdistrict !== null ? this.user.subdistrict.id  || this.user.subdistrict : null,
                    "postcode"              : this.user.item.postcode,
                    "check_policy"          : this.step_one,
                }
        
                    await Swal.fire({
                        title: 'คุณต้องการบันทึกข้อมูลใช่หรือไหม ?',
                        showDenyButton: false,
                        showCancelButton: true,
                        confirmButtonText: 'บันทึก',
                        cancelButtonText: `ยกเลิก`,
                    }).then(async (result)  => {
                    /* Read more about isConfirmed, isDenied below */
                        if (result.isConfirmed) {

                            let path = await `/api/user/register`
                            let response = await axios.post(`${path}`, fd)

                            if(response){

                                await this.insertComplain(response.data.register_id)
                                
                            }

                            await Swal.fire('บันทึกข้อมูลเรีบร้อยเเล้ว', '', 'success')
                        // } else if (result.isDenied) {
                        //     Swal.fire('Changes are not saved', '', 'info')
                        }
                    })

           } catch (error) {
            console.log(error);
           }
        },
        async insertComplain(register_id){
            let fd = await {
                "name"              : this.employee.name,
                "lastname"          : this.employee.lastname,
                "register_id"       : register_id,
                "division"          : this.employee.division,
                "description_face"  : this.employee.description_face,
                "topic"             : this.employee.complain_topic,
                "location"          : this.employee.complain_location,
                "start_date"        : `${moment(this.employee.start_date).format('YYYY-MM-DD')}`,
                "end_date"          : `${moment(this.employee.end_date).format('YYYY-MM-DD')}`,
                "detail"            : this.employee.complain_detail,
                "create_by"         : register_id,
                // "create_date"       : this.employee.files,
                "modified_by"       : register_id,
                // "modified_date"     : this.employee.files,
            }

            let path = await `/api/user/complain`
            let response = await axios.post(`${path}`, fd)

            if(response){

                   for (let i = 0; i < this.employee.files.length; i++) {

                    let number = await i + 1

                    await this.insertFile(register_id, response.data.complain_id, this.employee.files[i], number)
                    
                  
                }
            }
        },
        async insertFile(register_id, complain_id, file, id){

            try {

                const arr_file = await file.name.split(".")

                let file_name = await ''
                
                if(file.type === 'image/jpeg' || file.type === 'image/jpg' || file.type === 'image/png'){

                  file_name = await 'imgcid' + complain_id + '_' + id + '.' +arr_file[1] 

                }else if(file.type === 'application/pdf'){

                   file_name = await 'pdfcid' + complain_id + '_' + id + '.' +arr_file[1] 
                }

                let fd_upload = await {
                    "register_id"   : register_id,
                    "complain_id"   : complain_id,
                    "file_original" : file.name,
                    "file_name"     : file_name
                }

                    let path = await `/api/user/files`

                    let response = await axios.post(`${path}`, fd_upload )

                    if(response){
                        setTimeout(() => {this.myUpload(file_name,  file)}, 2000);
                    }

            } catch (error) {
                console.log(error);
            }

        },

        async myUpload(file_name, files){

            try {

                let fd_upload =  await new FormData();
                await fd_upload.append('image_name', file_name);
                await fd_upload.append('images', files);

           
                let res2  = await axios.post(`/api/user/uploadFiles`, fd_upload)
                
                console.log(res2);

            } catch (error) {

                console.log(error);
            }
        },

        login() {
            this.$router.push({name:'userLogin'})
        },
    },
  }
</script>
<style>
    .head-topic{
        background: #003366;
    }
    .head-left{
        color: white;
        font-size: 24px;
        padding: 1rem 0;
    }
    .h1{
        font-size: 30px;
    }
    .step{
        box-shadow: none!important;
    }
    .stepper-header{
        box-shadow: none;
    }
    .form-register{
        box-shadow: none!important;
    }
    .text-white span{
        color: white;
    }
    .btn-next{
        width: 100px;
        border-top-left-radius: 0px;
        border-bottom-left-radius: 0px;
    }
    .btn-back{
        width: 100px;
        border-top-right-radius: 0px;
        border-bottom-right-radius: 0px;
        border: 1px solid #003366;
        background: white;
        color: #003366;
    }

    ::v-deep .v-icon{
        font-size: 14px!important;
    }
   
</style>