<style lang="less">
  @import "../../styles/common.less";
</style>
<template>
  <div>
    <Modal v-model="modal1" title="讲座信息" width="900">
      <Form ref="formImage" :model="lecture"  :label-width="100">
        <FormItem label="id" prop="lectureId" v-if="this.$route.params.lectureId">
          <InputNumber v-model="lecture.lectureId" disabled />		    </FormItem>

        <FormItem label='教师' prop="teacher" style="width: 200px" >
          <Input v-model="lecture.teacher"  />
        </FormItem>

        <FormItem label="地点" prop="place" style="width: 200px">
          <Input v-model="lecture.place" />
        </FormItem>

        <FormItem label="时间" prop="time" >
<!--          <Input v-model="lecture.time" />-->
          <Date-picker v-model="lecture.time" type="datetime" format="yyyy-MM-dd HH:mm:ss" @on-change="handleDateChange" placeholder="选择日期和时间" style="width: 200px"></Date-picker>
        </FormItem>

        <FormItem label="信息" prop="info" style="width: 500px">
          <Input v-model="lecture.info"  type="textarea" />
        </FormItem>

        <FormItem label="奖品" prop="prize" style="width: 500px">
          <Input v-model="lecture.prize" />
        </FormItem>

      </Form>
      <div slot="footer">
        <i-button type="primary" size="small"  @click="addOrEdit()">提交</i-button>
      </div>
    </Modal>
    <Row>
      <Input v-model="searchName" placeholder="请输入讲座信息" style="width: 200px" />
<!--      <span @click="handleSearch" style="margin: 0 10px;"><Button type="primary" icon="search">{{'搜索'}}</Button></span>-->
<!--      <Button @click="handleCancel" type="default" >{{取消}}</Button>-->
      <Button @click="handleCreateOrEditItem(null)" type="primary" style="margin-right: 20px;float:right" icon="new">{{'新增'}}</Button>
    </Row>
    <Row class="margin-top-10 searchable-table-con1" style="width:100%">
      <Table :columns="columns"  :data="dataList" ></Table>
      <Row type="flex" justify="end" align="middle">
        <Page :total="dataCount"   show-elevator show-total style="padding: 10px;"></Page>
      </Row>
    </Row>
  </div>
</template>

<script>
  import axios from 'axios';
  import Qs from 'qs';
  axios.defaults.baseURL='http://localhost:8081'
  // axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
  export default {
    name: 'lecture',
    data(){
      return {
        dataCount:0,
        responseData : null,
        dataList: [],
        showData:[],
        modal1:false,
        temp:'',
        timeChange:false,
        lecture:{
          lectureId: null,
          teacher:'',
          place:'',
          time:'',
          info:'',
          prize:''
        },
        columns:[
          {
            key: "lectureId",
            title: 'id',
            width: '80px'
          },
          {
            key: "teacher",
            title: '教师'
          },
          {
            key: "time",
            title: '时间',
          },
          {
            key: "info",
            title: '详情',
            render:(h,params)=> {
              return h('span',{
                style: {
                  display: 'inline-block',
                  width: '100%',
                  overflow: 'hidden',
                  textOverflow: 'ellipsis',
                  whiteSpace: 'nowrap'
                },
                attrs: {title:  params.row.info}
              },params.row.info)
            }
          },
          {
            key: "prize",
            title: '奖品'
          },
          {
            title: '操作',
            align: "center",
            width: 190,
            key: "handle",
            render: (h, params) => {
              return h("div", [
                h(
                  "Button",
                  {
                    props: {
                      type: "primary",
                      size: 'small'
                    },
                    on: {
                      click: e => {
                        e.stopPropagation();
                        this.temp = params.row.time
                        this.handleCreateOrEditItem(params.row);
                      }
                    }
                  },
                  '编辑'
                ),
                h(
                  "Button",
                  {
                    style: {
                      margin: "0 5px"
                    },
                    props: {
                      type: "error",
                      placement: "top",
                      size: 'small',
                    },
                    on:{
                      click : e => {
                        e.stopPropagation()
                        this.$Modal.confirm({
                          title: '删除',
                          content: '是否确认删除该条数据',
                          onOk: () => {
                            this.delete(params.row.lectureId);
                          },
                          onCancel: () => {

                          }
                        });
                      },
                    }
                  },
                  '删除'
                )
              ]);
            }
          }
        ],
        pageSize: 10,
        searchName : '',
        currentPageNum: 0,
        columns2:[]
      }
    },
    methods: {
      getAllLecture () {
        console.log('into getData')
        axios.get('/lecture/getAllLecture')
          .then(res =>{
            this.responseData = res;
            console.log(res);
            this.dataList = res.data;
            this.dataCount = this.dataList.length;
            // this.dataFormat(this.dataList)
          })
      },
      handleCancel() {
        this.searchUsername = null;
        this.init();
      },
      // handlePageChange(pageNum) {
      //   this.currentPageNum = pageNum;
      //   this.search({
      //     size: this.pageSize,
      //     page: pageNum - 1,
      //     "username.contains": this.searchUsername
      //
      //   });
      //
      // },
      delete(id){
        console.log(id)
      },
      handleCreateOrEditItem (param) {
        console.log(param)
        // this.modal1 = true;
        if (param == null){
          console.log('1')
          this.lecture ={
            lectureId: null,
            teacher:'',
            place:'',
            time:'',
            info:'',
            prize:''
          }
          this.modal1 = true;
        }else {

          this.lecture = deepCopy(param)
          this.modal1 = true;
        }

      },
      init(){
        this.getAllLecture();
        this.timeChange = false;
      },
      // myFormatDate(date) {
      //   // console.log('into formate')
      //   if (!date || date == '')
      //     return '暂未填写'
      //   let dates = date.split('T')
      //   let res = dates[0]
      //   dates = dates[1].split('.')
      //   res += ' ' + dates[0]
      //   // console.log(res)
      //   return res
      // },
      addOrEdit(){
        console.log('into fun')
        console.log(this.lecture)
        if (this.lecture.lectureId == null){
          console.log('into add')
          console.log(this.lecture)
          axios.post('/lecture/addLecture',this.lecture)
            .then(res => {
              console.log('add success')
              this.init()
            })
            .catch(err => {
              console.log(err)
              console.log('add err')
            })
        }else {
          if (!this.timeChange)
            this.lecture.time = this.temp

          axios.post('/lecture/editLecture',this.lecture)
            .then(res => {
              console.log('update success')
              this.init()
            })
            .catch(err => {
              console.log(err)
              console.log('update err')
            })
        }
        this.modal1 = false

      },
      handleDateChange(dateTime){
        this.lecture.time = dateTime
        this.timeChange = true
      }
    },
    mounted() {
      this.init();
    },
    created(){
      this.init();
    },
  }
  function deepCopy(source) {
    console.log('into deepCopy')
    console.log(source)
    return JSON.parse(JSON.stringify(source))
  }

</script>

<style scoped>

</style>
