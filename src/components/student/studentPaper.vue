<template>
    <section id="exam">
        <h2> 试卷名称
            <el-button round @click="submit">提交</el-button>

        </h2>
        <el-form :model="form" ref="form">
            <ul class="box">
                <!--{{tableData}}-->
                <li v-for="(item,index) in tableData" :key="index">
                    <h3> {{item.title}}  <span>({{item.score}}分)</span></h3>
                    <ul class="options" v-if="item.type == 1">
                        <el-form-item>
                            <el-radio-group v-model="form['title'+index]">
                                <el-radio v-for="v in item.options" :key="v['keys']" :label="v['keys']"
                                >{{v['value']}}--{{index}}
                                </el-radio>
                            </el-radio-group>
                        </el-form-item>
                    </ul>
                    <ul class="options" v-else-if="item.type == 2">
                        <el-form-item>
                            <el-checkbox-group v-model="form['title'+index]">
                                <el-checkbox v-for="v in item.options" :key="v['keys']" :label="v['keys']">{{v['value']}}--{{index}}</el-checkbox>
                            </el-checkbox-group>
                        </el-form-item>
                    </ul>
                    <ul class="options" v-else-if="item.type == 3">
                        <li>
                            <el-input v-model="form['title'+index]"></el-input>
                        </li>
                    </ul>
                </li>
            </ul>
        </el-form>

        <!--  <el-form :model="form" ref="form">
              <el-form-item v-for="(item,index) in tableData" :key="index" >
                      <el-radio-group v-if="item.type==1" v-model="form['title'+index]">
                          <el-radio v-for="v in item.options" :key="v['keys'+index]" :label="v['keys']">{{v['value']}}&#45;&#45;{{index}}</el-radio>
                      </el-radio-group>
                      <el-checkbox-group v-else-if="item.type==2" v-model="form['title'+index]">
                          <el-checkbox v-for="v in item.options" :key="v['keys']" :label="v['keys']">{{v['value']}}&#45;&#45;{{index}}</el-checkbox>
                      </el-checkbox-group>
                      <el-input v-else-if="item.type==3"v-model="form['title'+index]"></el-input>
              </el-form-item>
          </el-form>-->
    </section>
</template>
<script>
    import ElCheckbox from "../../../node_modules/element-ui/packages/checkbox/src/checkbox.vue";
    import ElRadio from "../../../node_modules/element-ui/packages/radio/src/radio.vue";
    import ElFormItem from "../../../node_modules/element-ui/packages/form/src/form-item.vue";
    export default {
        components: {
            ElFormItem,
            ElRadio,
            ElCheckbox},
        name: 'studentPaper',
        data: function () {
            return {
                tableData5: [],
                pid: '',
                form: {
                    title0:'',
                    title1:'',
                    title2:'',
                    title3:'',
                    title4:'',
                    title5:'',
                }
            }
        },
        methods: {
            submit() {
                let tids = this.tableData5.map(ele=>ele.tid);
                this.$http.post('/home/insertRecord.php',{tids,pid:this.pid,answer:this.form}).then(res=>{
                })
            }
        },
        computed: {
            tableData() {
                let tableData = [];
                this.tableData5.forEach(element => {
                    let v = element.title.split('_');
                    let title = v[0];
                    let options = [];
                    if (v[1]) {
                        let arr = v[1].split('|');
                        arr.forEach(ele => {
                            options.push({keys: ele.split(':')[0], value: ele.split(':')[1]});
                        })
                    } else {
                        options = '';
                    }
                    tableData.push({title, options, score: element.score, type: element.type})
                });
                return tableData;
            }
        },
        mounted() {
            this.pid = this.$route.query.pid;
            let index;
            this.$http.get('/home/studentPaper.php?pid=' + this.pid).then(res => {
                this.tableData5 = res.body.sort((a, b) => a.type - b.type);
                for(let i=0;i< this.tableData5.length;i++){
                    this.form['title' + index] = '';
                }
                /*this.tableData5.forEach((ele, index) => {
                    if (ele.type == 1 || ele.type == 3) {
                    } else if (ele.type == 2) {
                        this.form['title' + index] = [];
                    }
                })*/
                for(let i=0;i<this.tableData5.length;i++){
                    this.form['title'+index]='';
                }
            })
        }
    }
</script>
<style>
    * {
        margin: 0;
        padding: 0;
        list-style: none;
    }
    #exam {
        padding-top: 100px;
        width: 1200px;
        height: auto;
        margin: 0 auto;
    }
    #exam > h2 {
        width: 1200px;
        height: 50px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 10px;
        background: #dddddd;
        font-size: 14px;
        font-weight: normal;
        position: fixed;
        top: 100px;
        box-sizing: border-box;
    }
    .box {
        width: 100%;
        height: auto;
        margin-top: 70px;
    }
    .box > li {
        width: 100%;
        height: 200px;
        margin: 15px 0;
        padding: 30px;
        box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
        border-radius: 5px;
    }
    .box li h3 {
        font-size: 16px;
        border-bottom: 1px solid #999999;
        line-height: 1.5;
        padding: 5px 0;
        margin-bottom: 10px;
    }
    .options li {
        height: 40px;
        line-height: 40px;
    }
</style>