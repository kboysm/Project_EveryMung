<template>
  <v-row justify="center">
     <v-col>
    <vue-good-table
    :columns="columns"
    :rows="rows"
    @on-row-click="onRowClick"
    max-height="500px"
    :line-numbers="true"
    :search-options="{
    enabled: true,
    }"
>  </vue-good-table>
<v-dialog v-model="dialog" persistent max-width="600px">
      <template v-slot:activator="{ on }">
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">상세정보</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <span>예약자</span>
            <v-row>
              <v-col cols="12">
                <v-text-field label="Name"  v-model="home.userName" readonly ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Email" v-model="home.userEmail" readonly ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Phone" v-model="home.userPhone" readonly ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Address" v-model="home.userAddress" readonly ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Date" v-model="home.startTime" readonly></v-text-field>
              </v-col>
              <span class="ml-3">시터</span>
              <v-col cols="12">
                <v-text-field label="SitterName" v-model="home.sitterName" readonly></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Phone" v-model="home.sitterPhone" readonly></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Email" v-model="home.sitterEmail" readonly></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Type" v-model="home.sittingType" readonly></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <!-- <v-card-text v-if="deleteAlert">
            <v-alert v-model="deleteAlert" type="warning">
              <h4>정말 삭제 하시겠습니까?</h4>
              <v-btn class="mr-4"  color="error" @click="delet(user.userNo)">확인</v-btn>
              <v-btn color="secondary" @click="deleteAlert=false">취소</v-btn>
            </v-alert>
        </v-card-text>
        <v-card-text v-if="updateAlert">
            <v-alert v-model="updateAlert" type="warning">
              <h4>정말 수정 하시겠습니까?</h4>
              <v-btn class="mr-4"  color="error" @click="update()">확인</v-btn>
              <v-btn color="secondary" @click="updateAlert=false">취소</v-btn>
            </v-alert>
        </v-card-text> -->
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">확인</v-btn>
          <!-- <v-btn color="blue darken-1" text @click.native="deleteAlert=true">삭제</v-btn>
          <v-btn color="blue darken-1" text @click.native="updateAlert=true">수정</v-btn> -->
        </v-card-actions>
        
        
      </v-card>
    </v-dialog>

  </v-col>
  </v-row>
</template>
<script>
import 'vue-good-table/dist/vue-good-table.css'
import { VueGoodTable } from 'vue-good-table'

export default {
    components: {
  VueGoodTable,
},data(){
    return {
       dialog: false,
       deleteAlert: false,
       updateAlert: false,
       home:{
       userName :'',
       userEmail:'',
       userPhone:'',
       userAddress:'',
       paymentNo:'',
       startTime:'',
       endTime:'',
       sitterName:'',
       sitterPhone:'',
       sitterEmail:'',
       sittingType:'',
       },
      columns: [
        {
          label: '이름',
          field: 'userName',
          width: '80px',
        },
        {
          label: '이메일',
          field: 'userEmail',
        },
        {
          label: '휴대번호',
          field: 'userPhone',
        },
        {
          label: '주소',
          field: 'userAddress',
        },
        {
          label: '예약날짜',
          field: 'startTime',
          width: '110px',
        },
        {
          label: '예약시간',
          field: 'endTime',
        },
        {
          label: '시터',
          field: 'sitterName',
          width: '80px',
        },
        {
          label: '시터번호',
          field: 'sitterPhone',
        },
        {
          label: '타입',
          field: 'sittingType',
          width: '80px',
        },
        
      ],
      rows:[],
    };
  },
  created() {

    this.$http.get(`http://localhost:1234/countPay`)
        .then(res => {
          let lsm='' //endTime
          let timeG=''
                for(let sitterT in res.data){
                  if(res.data[sitterT].sittingType==="home"){
                  lsm=String(res.data[sitterT].startTime).split(" ")
                  res.data[sitterT].startTime=lsm[0]
                  
                  timeG=lsm[1]
                  timeG+="~"+String(res.data[sitterT].endTime).split(" ")[1]
                  console.log(lsm[1])
                  res.data[sitterT].endTime=timeG
                  
                  
                  this.rows.push(res.data[sitterT])
                  console.log(this.rows)
                  
                  }
                  }


                })
                .catch(err => { 
                alert(err+"\n"+"HomeCare(created) 에러")
              });
              

  },
  methods: {
  onRowClick(params) {
     this.dialog=true
     this.home.userName = params.row.userName
     this.home.userEmail = params.row.userEmail
     this.home.userPhone = params.row.userPhone
     this.home.userAddress = params.row.userAddress
     this.home.userProfile = params.row.userProfile
     this.home.startTime = params.row.startTime
     this.home.sitterName = params.row.sitterName
     this.home.sitterPhone = params.row.sitterPhone
     this.home.sitterEmail = params.row.sitterEmail
     this.home.sittingType = params.row.sittingType

  },
  delet(userNo){
     this.dialog=false
     this.deleteAlert=false
     const No = userNo
     
      this.$http.post(`http://localhost:1234/deleteUser/${No}`).then(res =>{
        const idx = this.rows.findIndex(x => x.userNo === userNo)
        console.log(idx)
              this.dialog=false
              this.rows.splice(idx, 1)
      }).catch(err =>{
        alert(err+"\n"+"HomeCare(delet) 에러")
      })

  },
  update(){
     this.dialog=false
     this.updateAlert=false
     this.$http.post('http://localhost:1234/updateUser',this.user) 
              .then(res => { 
                this.selectAll();
              }) 
              .catch(err => { 
                alert(err+"\n"+"HomeCare(update) 에러")

              });
  },
    },

 
}
</script>