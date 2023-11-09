<template>
    <div class="f_c_main" style="position:relative">
      <div style=""  class="f_c_box">
        <div ref="form" class="f_c_form">
          <el-form
            :inline="true"
            :model="billForm"
            ref="billForm"
            :rules="rules"
            label-width="130px"
            class="demo-Form"
          >
            <div class="titlecontent">    
              <p class="bigtitle"></p>
            </div>
            <div  class="o_i_btn">
              <el-button type="primary" :loading="saveloading" @click="save" style="margin-left:20px">保存</el-button>
              <el-upload
                action=""
                class="upload-excel"
                :on-change="handleImport"
                multiple
                :show-file-list="false"
                :auto-upload="false"
                >
                <el-button type="primary" style="margin-left:20px">上传附件</el-button>
              </el-upload>
              <el-button type="primary"   style="margin-left:20px" @click="billFun">引用客户费用单</el-button>
            </div>
            <div style="padding-top:10px">
                <el-form-item prop="orgCode" label="财务组织">
                <el-select
                  @focus="orgCodeFocus"
                  v-model="billForm.orgCode"
                  clearable
                  filterable
                >
                  <el-option
                    v-for="(item, index) in organizationlist"
                    :key="index"
                    :label="item.companyName"
                    :value="item.company"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item prop="billDate" label="单据日期">
                <el-date-picker
                  v-model="billForm.billDate"
                  type="date"
                  placeholder="选择日期"
                  format="yyyy-MM-dd"
                  value-format="yyyy-MM-ddTHH:mm:ss"
                >
                </el-date-picker>
              </el-form-item>
              <el-form-item label="单据号">
                <el-input v-model="billForm.billNo"></el-input>
              </el-form-item>
              <el-form-item label="客户">
                <el-select
                  v-model="billForm.customer"
                  placeholder="请选择"
                  clearable
                  filterable
                  @focus="customerFocus"
                  @change="customerChange"
                  popper-class="customer"
                >
                  <el-option
                    :label="item.customerNameCode"
                    :value="item.customerCode"
                    :key="index"
                    v-for="(item, index) in customlist"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item prop="divisionCode" label="事业部">
                <el-select
                  clearable
                  filterable
                  placeholder="请选择"
                  v-model="billForm.divisionCode"
                  @focus="businessUnitFocus"
                  popper-class="businessunit"
                >
                  <el-option
                    v-for="item in businessUnitData"
                    :key="item.businessUnitCode"
                    :label="item.businessUnitNameCode"
                    :value="item.businessUnitCode"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item prop="billDate" label="业务日期">
                <el-date-picker
                  v-model="billForm.billDate"
                  type="date"
                  placeholder="选择日期"
                  format="yyyy-MM-dd"
                  value-format="yyyy-MM-ddTHH:mm:ss"
                >
                </el-date-picker>
              </el-form-item>
              <el-form-item prop="billtype" label="单据类型">
                <el-select
                  clearable
                  filterable
                  placeholder="请选择"
                  v-model="billForm.billtype"
                >
                  <el-option
                    v-for="item in billtypeList"
                    :key="item.code"
                    :label="item.name"
                    :value="item.name"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item prop="currency" label="币种">
                <el-select
                  clearable
                  filterable
                  placeholder="请选择"
                  v-model="billForm.currency"
                >
                  <el-option
                    v-for="item in currencyList"
                    :key="item.code"
                    :label="item.name"
                    :value="item.code"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="含税原币金额">
                <el-input v-model="billForm.billNo" disabled></el-input>
              </el-form-item>
              <el-form-item label="渠道结算号">
                <el-input v-model="billForm.billNo"></el-input>
              </el-form-item>
              <el-form-item prop="billDate" label="制单日期">
                <el-date-picker
                  v-model="billForm.billDate"
                  type="date"
                  placeholder="选择日期"
                  format="yyyy-MM-dd"
                  value-format="yyyy-MM-ddTHH:mm:ss"
                >
                </el-date-picker>
              </el-form-item>
              <el-form-item label="制单人">
                <el-input v-model="billForm.billNo" disabled></el-input>
              </el-form-item>
              <el-form-item label="NC发票号">
                <el-input v-model="billForm.billNo" disabled></el-input>
              </el-form-item>
              <el-form-item label="OA流程单号">
                <el-input v-model="billForm.billNo" disabled></el-input>
              </el-form-item>
              <el-form-item label="备注">
                <el-input v-model="billForm.remark" type="textarea" clearable maxlength="1000"></el-input>
              </el-form-item>
              <el-form-item label="发票附件">
                <div >
                    <p style="height:20px;line-height:20px;margin-top:5px;margin-bottom:20px" v-for="(item,index) in filesTable" :key="index">
                        <span>
                            <a :href="item.ossUrl" style='vertical-align:middle;white-space:nowrap;text-overflow:ellipsis;overflow:hidden;'>{{item.originFileName}}</a>
                        </span>
                        <span class="el-icon-error" v-if="!finishDisable" @click="deleteFun(index, item)" style="color:blue;cursor: pointer"></span>
                    </p>
                </div>
              </el-form-item>
            </div>
          </el-form>
          <!-- <div class="unified_title">附件区</div> -->
        <div class="last_btn">
          <span>摘要说明:发票信息需要登录oa发票云,识别发票信息后此用于此单引用</span>
          <el-button type="primary" @click="addRow" >增行</el-button>
          <el-button type="primary" @click="deleteTable">删行</el-button>
        </div>
        <div class="custominfo" style="margin-top: 20px">
          <div class="main">
            <el-table
              max-height="550"
              ref="multipleTable"
              style="width: 100%"
              border
              v-loading="loading"
              :data="tableData"
              :row-key="(row) => row.index" 
              @selection-change="handleSelectionChange"
            >
              <div slot="empty" class="kc_table_empty">暂无数据</div>
              <el-table-column
                type="selection"
                :reserve-selection="true"
                align="center"
                fixed
                width="55"
              >
              </el-table-column>
              <el-table-column type="index" label="序号" align="center" width="55"> </el-table-column>
              <el-table-column prop="coreId" label="核心id" min-width="110">
              <template slot="header" slot-scope="props">
                <span class="star">*</span>
                <span>核心id</span>
              </template>
              <template slot-scope="props">
                <el-input
                  type="text"
                  v-model="props.row.coreId"
                  @focus='coreFocus(props.$index,props.row)'
                  clearable
                  style='width:90px'
                ></el-input>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="businessDate"
              min-width="200"
              label="业务日期">
              <template slot="header" slot-scope="props">
                <span class="star">*</span>
                <span>业务日期</span>
              </template>
              <template slot-scope="scope">
                <el-date-picker
                    v-model="scope.row.businessDate"
                    type="date"
                    placeholder="选择日期"
                    format="yyyy-MM-dd"
                    value-format="yyyy-MM-ddTHH:mm:ss"
                    style='width:150px'
                >
                </el-date-picker>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="remark"
              min-width="210"
              label="摘要">
              <template slot-scope="changeNotescope">
                <el-input
                    v-model="changeNotescope.row.remark"
                    @change="changeNote(changeNotescope.row)"
                    placeholder="请输入"
                    clearable
                  ></el-input>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="inoutCode"
              width="140"
              label="收支项目">
              <template slot-scope="scope">
                <el-select
                    filterable
                    clearable
                    placeholder="请选择"
                    v-model="scope.row.inoutCode"
                    @change="inoutCodeChange(scope.$index,scope.row)"
                    :popper-class="'inoutCode' + scope.$index"
                  >
                    <el-option
                      v-for="item in inoutCodeData"
                      :key="item.code"
                      :label="item.name"
                      :value="item.code"
                    ></el-option>
                  </el-select>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>收支项目</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="productLineCode"
              min-width="210"
              label="产品线">
              <template slot-scope="scope">
                <el-select
                    filterable
                    clearable
                    placeholder="请选择"
                    v-model="scope.row.productLineCode"
                    @change="productLineCodeChange(scope.row)"
                  >
                    <el-option
                      v-for="item in productLineData"
                      :key="item.productLineCode"
                      :label="item.productLineNameCode"
                      :value="item.productLineCode"
                    ></el-option>
                  </el-select>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>产品线</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="fylx"
              min-width="210"
              label="费用类型">
            </el-table-column>
            <el-table-column
              align="center"
              min-width="210"
              prop="threeChannelCode"
              label="三级渠道">
              <template slot-scope="scope">
                <el-select
                    filterable
                    clearable
                    placeholder="请选择"
                    v-model="scope.row.threeChannelCode"
                    @change="selectChannelCode(scope.row)"
                  >
                    <el-option
                      v-for="item in scope.row.threeChannelList"
                      :key="item.code"
                      :label="item.name"
                      :value="item.code"
                    ></el-option>
                  </el-select>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>三级渠道</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="remark"
              min-width="210"
              label="发票信息">
              <template slot-scope="scope">
                <el-input
                    v-model="scope.row.remark"
                    placeholder="请输入"
                    suffix-icon="el-icon-search"
                    @focus="invoiceInfo(scope.row)"
                    clearable
                  ></el-input>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="fylx"
              min-width="210"
              label="开票方">
            </el-table-column>
            <el-table-column
              align="center"
              prop="fylx"
              min-width="210"
              label="收货方">
            </el-table-column>
            <el-table-column
              align="center"
              prop="taxRateStr"
              min-width="210"
              label="税率">
              <template slot-scope="scope">
                  <el-select
                    :disabled="!scope.row.payType"
                    filterable
                    clearable
                    placeholder="请选择"
                    v-model="scope.row.taxRateStr"
                  >
                    <el-option v-show="scope.row.payType=='FI-001-06' || scope.row.payType=='FI-001-03'" label="6%" value="6%"></el-option>
                    <el-option v-show="scope.row.payType=='FI-001-01' || scope.row.payType=='FI-001-04' || scope.row.payType=='FI-001-09'" label="13%" value="13%"></el-option>
                    <el-option  label="0" value="0"></el-option>
                  </el-select>
              </template>
               <template slot="header">
                <i class="iconRed">*</i>
                <span>税率</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="productLineCode"
              min-width="210"
              label="币种">
              <template slot-scope="scope">
                <el-select
                    filterable
                    clearable
                    placeholder="请选择"
                    v-model="scope.row.currency"
                    @change="currencyChange(scope.row)"
                  >
                    <el-option
                      v-for="item in currencyList"
                      :key="item.code"
                      :label="item.name"
                      :value="item.code"
                    ></el-option>
                  </el-select>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>币种</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="productLineCode"
              min-width="210"
              label="原币价税合计">
              <template slot-scope="scope">
                <el-input v-model="scope.row.productLineCode"></el-input>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>原币价税合计</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="productLineCode"
              min-width="210"
              label="原币无税金额">
              <template slot-scope="scope">
                <el-input v-model="scope.row.productLineCode"></el-input>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>原币无税金额</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="productLineCode"
              min-width="210"
              label="税额">
              <template slot-scope="scope">
                <el-input v-model="scope.row.productLineCode"></el-input>
              </template>
              <template slot="header">
                <i class="iconRed">*</i>
                <span>税额</span>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="fylx"
              min-width="210"
              label="NC客户费用单单号">
            </el-table-column>
            </el-table>
          </div>
        </div>
        </div>
      </div>
      <!-- 核心对照 -->
      <div>
        <el-dialog
          custom-class="kc_common_dialog"
          title="核心对照"
          :visible.sync="dialogVisible_coreId"
          width="60%"
          :before-close="coreIdhandleClose"
        >
          <div style='margin-top:20px'>
            <CoreId  ref='coreId'></CoreId>
          </div>
          <span slot="footer" class="dialog-footer">
            <el-button class="kc_cancel_button" @click="coreIdCancel">取 消</el-button>
            <el-button type="primary" @click="coreIdConfirm">确 定</el-button>
          </span>
        </el-dialog>
      </div>
      <!-- 我的票夹 -->
      <div>
        <el-dialog
          custom-class="kc_common_dialog"
          title="我的票夹"
          :visible.sync="dialogVisible_invoiceInfo"
          width="60%"
          :before-close="coreIdhandleClose"
        >
          <div style='margin-top:20px'>
            <InvoiceInfo  ref='invoiceInfo'></InvoiceInfo>
          </div>
          <span slot="footer" class="dialog-footer">
            <el-button class="kc_cancel_button" @click="invoiceInfoCancel">取 消</el-button>
            <el-button type="primary" @click="invoiceInfoConfirm">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </div>
  </template>
  <script>
  import {
    deleteFile,
    submitC,
    customerList
  } from '@/api/billManage'
  import { mapState, mapActions } from 'vuex'
  import {inputBrandSubmitByBillNo,save,} from "@/api/accrualManagement"
  import { getcusTom, getBusiness, getProductline ,inoutCodeDropDown} from '@/api/basic_info'
  import InvoiceInfo from '@/views/components/invoiceInfo';
  import CoreId from '@/views/components/dropdownData'
  import func from '@/lib/commonfun'
  import Cookies from 'js-cookie'
  export default {
    name: 'zkinvoiceAdd',
    components:{InvoiceInfo,CoreId},
    data() {
      return {
        form: {},
        billForm: {
         
        },
        tableData:[{}],
        rules: {
          orgCode: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          billDate: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          customerCode: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          businessDate: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          writeOffType: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          rebateCycle: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          divisionCode: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
          currency: [
              { required: true, message: '请填写', trigger: 'blur' }
          ],
        },
        customerlistmore:[],
        filesTable: [],
        uploadText: '上传附件',
        upload_loading: false,
        
        customlist:[],//客户
        productLineData: [], // 产品线
        businessUnitData:[],//事业部
        billtypeList:[
            {name:'类型一',code:1},
            {name:'类型二',code:2},
        ],//单据类型
        billstateList:[],
        organizationlist:[],
        currencyList:[
           {name:'人民币',code:'CNY'},
           {name:'英镑',code:'GBP'},
           {name:'港币',code:'HKD'},
           {name:'日元',code:'JPY'},
           {name:'美元',code:'USD'},
           {name:'韩币',code:'KRW'},
        ],
        inoutCodeData:[],
        saveloading:false,
        loading:false,
        dialogVisible_invoiceInfo:false,
        dialogVisible_coreId:false,
        selectionItemIndexes:[]

      }
    },
    created(){
    },
    activated() {
      // 更新面包屑
      let breadcrumbList = [{ title: "渠道费用账单", url: "" }, { title: "账扣发票", url: "" },{ title: "账扣发票维护(说明：渠道账单中,账扣费用的收票管理)", url: "" }]
      this.$store.commit('updatePublicdata', { key: 'breadcrumbList', val: breadcrumbList })
      this.$store.commit('updatePublicdata', { key: 'isDropdown', val: false })
      this.tableKey = Math.random()
      this.getinoutCodeApi();
      this.productLineApi();
    },
    computed: {
       ...mapState({
          keepAliveList: state => state.router.keepAliveList,
      })
    },
    filters: {
      
    },
    watch: {
      
    },
    methods: {
          // 销售组织下拉
        orgCodeFocus() {
        getOrganization({}).then(res=>{
            console.log(res,'dssada');
            this.organizationlist = res.body
        })
        },
        // 获取客户
        customerFocus() {
            if (this.customlist.length < 1) {
                func.createLoading('.customer', this)
                getcusTom({}).then((res)=>{
                    this.loadingselect.close()
                    if(res.code==201){
                        let data=res.body;
                        data.map((item)=>{
                            item.customerNameCode=item.customerName+'['+item.customerCode+']';
                            return item;
                        })
                        this.customlist=data;
                    }else{
                        func.notifyFun(this, '提示', 'warning', res.message)
                    }
                })
            }
        },
        customerChange() {},
        businessUnitFocus(){
            // if (this.businessUnitData.length < 1) {
            //     func.createLoading('.businessunit', this)
            //     var data = {enableState:1}
            //     func.requestApi(getBusiness, data, 'businessUnitData', this)
            // }
            if (this.businessUnitData.length < 1) {
                func.createLoading('.businessunit', this)
                getBusiness({enableState:1}).then((res)=>{
                    this.loadingselect.close()
                    if(res.code==201){
                        let data=res.body;
                        data.map((item)=>{
                            item.businessUnitNameCode=item.businessUnitName+'['+item.businessUnitCode+']';
                            return item;
                        })
                        this.businessUnitData=data;
                    }else{
                        func.notifyFun(this, '提示', 'warning', res.message)
                    }
                })
            }
        },
        // 品牌应收收支项目
        getinoutCodeApi(){
        inoutCodeDropDown().then((res)=>{
            if(res.code==0){
            this.inoutCodeData=res.data;
            }else{
            func.notifyFun(this,'提示','warning',res.message);
            }
        })
        },
        inoutCodeChange(index,item){
        let obj = {};
        obj = this.inoutCodeData.find(item1 => {
            return item1.code === item.inoutCode;
        });
        console.log(obj);
        this.tableData[index].inoutName = obj == undefined ? '' : obj.name;
        this.tableData[index].inoutCode = obj == undefined ? '' : obj.code;
        },
         // 产品线
        productLineApi(index) {
          getProductline({enableState:"1"}).then(res => {
            if (res.code == '201') {
              let productLineData=res.body;
              productLineData.map((item)=>{
                item.productLineNameCode=item.productLineName+'['+item.productLineCode+']';
                return item;
              })
              this.productLineData = productLineData;
            }
          })
        },
        productLineCodeChange (item) {
            console.log(item)
            this.$set(item, 'coreId', '');
            this.$set(item, 'productLineCode', item.productLineCode)
            let obj = this.productLineData.find((el) => {
                return el.productLineCode === item.productLineCode
            })
            let productLineName = obj === undefined ? '' : obj.productLineName
            this.$set(item, 'productLineName', productLineName)
            this.getClearVerifyPeriod(item);
        },
        selectChannelCode(item){
            this.$set(item, 'coreId', '');
            this.$set(item, 'threeChannelCode', item.threeChannelCode)
            let obj = this.threeChannel.find((el) => {
                return el.code === item.threeChannelCode
            })
            let threeChannelName = obj === undefined ? '' : obj.name
            this.$set(item, 'threeChannelName', threeChannelName)
        },
             // 核销周期清空处理
        getClearVerifyPeriod(item){
            this.$set(item,'cycleListOption',[])
            this.$set(item,'writeOffPeriodList',[])
            this.$set(item,'writeOffPeriod','')//核销账期
            this.$set(item,'gatheringPeriod','')//回款账期
            this.$set(item,'cycleName','')
            this.$set(item,'cycleCode','')
            this.$set(item, 'financePersonName', '')
        },
        save(){
            this.$refs['billForm'].validate((valid) => {
                if (valid) {
                if(this.billForm.writeOffType ==3 && this.filesTable.length<1){
                    func.notifyFun(this, '提示', 'warning', '请上传附件')
                }else{
                    let data = {
                    isSubmit:0
                    }
                    let params = {
                        ...this.billForm,
                        fileInfoPoList:this.filesTable,
                        submitItemList:this.tableData,
                        creatorName: JSON.parse(sessionStorage.getItem('logoninfo')).name
                    }
                    // console.log(this.tableData,'tableData');
                    save(params,data).then(res=>{
                    if(res.code ==0){
                        func.notifyFun(this, "提示", "success", "提交成功")
                        this.$router.push({ path: '/contractRebate'})
                    }else{
                        func.notifyFun(this, '提示', 'warning', res.message)
                    }
                    })
                }
                
                } else {
                return false;
                }
            });
            
        },
        // 增行
        addRow () {
            const list = {
            ids:Math.random(),
            taxAmount:'',
            taxRate:'',
            taxCode:'',
            functionalAmount:'',
            originalAmount:'',
            currency:'',
            payType:'',
            threeChannelName:'',
            threeChannelCode:'',
            customerName:'',
            customerCode:'',
            rebate:'',
            productLineName:'',
            productLineCode:'',
            remark:'',
            businessDate:this.billForm.businessDate,
            inoutCode:'001',
            cycleListOption:[],
            writeOffPeriodList:[]
            }
            this.tableData.unshift(list);
        },
        deleteTable(){
          let selectionItemIndexes=this.selectionItemIndexes;
          if(selectionItemIndexes.length>0){
            this.$confirm('确定删除吗?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(err => {
              let arr=[];
              this.tableData.map((item,index)=>{
                if(selectionItemIndexes.indexOf(item.ids)==-1){
                  arr.push(item);
                }
              })
              this.tableData=arr;
              // func.notifyFun(this, '提示', 'success', '删除成功!');
                this.$message({
                  type: 'success',
                  message: '删除成功!'
                });
            })
          }
        },
        // 明细多选
        handleSelectionChange(val) {
            this.choosedetailList = val
            this.selectionItemIndexes = []
            val.forEach((item) => {
              this.selectionItemIndexes.push(item.ids)
            })  
        },
      // 文件多选
        handleSelectionfilesChange(val) {
            this.files = val
        },
        // 操作按钮
        operationBind(e) {
            var logoninfo = JSON.parse(sessionStorage.getItem('logoninfo'))
        },
        closeBind() {
            this.$router.go(-1)
        },
        // 上传文件
        uploadFJ(e) {
            console.log(e)
            let files = e
            let filist = []
            let formData = new FormData()
            formData.append('file',files);
            let query = {
                billNo:this.billForm.billNo,
                createBy:Cookies.get('userName')
            }
            console.log(formData,'2121');
            //    console.log(formData.values())
            //    formData.append('file',files);
            this.uploadText = '导入中...'
            this.upload_loading = true
            inputBrandSubmitByBillNo(formData,query).then(
                res => {
                this.uploadText = '导入'
                this.upload_loading = false
                if (res.code == 0) {
                    func.notifyFun(this, '提示', 'success', '上传成功!')
                    // this.getDetailData();
                    this.filesTable = res.data
                } else {
                    func.notifyFun(this, '提示', 'warning', res.message)
                }
                },
                err => {
                this.upload_loading = false
                this.uploadText = '导入'
                func.notifyFun(this, '提示', 'warning', err.response.data.message)
                }
            )
        },
        // 删除附件
        deleteFun(index, item) {
            this.$confirm('确定删除吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(err => {
            let params = {
                fileId: item.id,
                channelBillId: item.billId,
                operatorName: Cookies.get('userName'),
                operatorCode: Cookies.get('loginname')
            }
            deleteFile(params).then(
                res => {
                if (res.code == '0') {
                    func.notifyFun(this, '提示', 'success', '删除成功!')
                    // this.getDetailData();
                    console.log(index)
                    this.filesTable.splice(index, 1)
                } else {
                    func.notifyFun(this, '提示', 'warning', res.message)
                }
                },
                err => {
                func.notifyFun(this, '提示', 'warning', err.response.data.message)
                }
            )
            })
        },
        //保存
        submit(val) {
            this.$refs['billForm'].validate(valid => {
            if (valid) {
                let params = { ...this.billForm, itemDtoList: this.tableData }
                let data = { isSubmit: val }
                const loading = this.$loading({
                lock: true,
                text: 'Loading',
                spinner: 'el-icon-loading',
                background: 'rgba(0, 0, 0, 0.7)'
                })
                submitC(params, data).then(res => {
                loading.close()
                // console.log(res,'dsads');
                if (res.code == 0) {
                    func.notifyFun(this, '提示', 'success', '提交成功!');
                    this.$router.go(-1)
                    func.deleteRouter(this,'contractRebateAdd');
                } else {
                    func.notifyFun(this, '提示', 'warning', res.message)
                }
                }).catch(() => {
                    loading.close()
                })
            } else {
                console.log('error submit!!')
                return false
            }
            })
        },
        handleImport (file) {
            let fileName = file.raw.name
            let fileType = fileName.substring(fileName.lastIndexOf('.') + 1);
            // 判断上传文件格式
            if (file.raw) {
                this.uploadFJ(file.raw)
            } else {
            func.notifyFun(this, "提示", "warning", "请上传附件")
            }
        },
          //核心对照弹窗
        coreFocus(index,item){
        if(this.billForm.customerCode==='' || !this.billForm.customerCode){
            func.notifyFun(this,'提示','warning','品牌客户名称不能为空!')
            return;
        }
        if(this.billForm.divisionCode==='' || !this.billForm.divisionCode){
            func.notifyFun(this,'提示','warning','事业部不能为空!')
            return;
        }
        this.dialogVisible_coreId=true;
        this.clickIndex=index;
        console.log(item)
        let obj={
            supplierName:this.billForm.customerName?this.billForm.customerName:'',
            supplierCode:this.billForm.customerCode?this.billForm.customerCode:'',//供应商
            businessUnitName:this.billForm.divisionName?this.billForm.divisionName:'',//事业部
            businessUnitCode:this.billForm.divisionCode?this.billForm.divisionCode:'',
            customerName:item.customerName?item.customerName:'',
            customerCode:item.customerCode?item.customerCode:'',//平台渠道
            threeChannelName:item.threeChannelName?item.threeChannelName:'',
            threeChannelCode:item.threeChannelCode?item.threeChannelCode:'',//三级渠道
            productLineCode:item.productLineCode?item.productLineCode:'',//产品线
            productLineName:item.productLineName?item.productLineName:''
        }
        setTimeout(()=>{
            this.$refs.coreId.getData(JSON.stringify(obj))
        })
        },
        coreIdCancel(){
        this.dialogVisible_coreId=false;
        },
        coreIdConfirm(){
        this.dialogVisible_coreId=false;
        let chooseData=this.$refs.coreId.chooseData;
        console.log(chooseData)
        let item=this.tableData[this.clickIndex];
        this.$set( item,'coreId',chooseData.coreId)
        this.$set( item,'threeChannelName',chooseData.threeChannelName)
        this.$set( item,'threeChannelCode',chooseData.threeChannelCode)
        this.$set( item,'productLineName',chooseData.productLineName)
        this.$set( item,'productLineCode',chooseData.productLineCode)
        this.$set( item,'customerName',chooseData.customerName)
        this.$set( item,'customerCode',chooseData.customerCode)
        let enterprise=[{code:chooseData.customerCode, name: chooseData.customerName,customerNameCode:'['+chooseData.customerCode+']'+chooseData.customerName}];
        this.$set(item,'enterprise',enterprise);//渠道下拉数据
        let threeChannelList=[{code:chooseData.threeChannelCode, name: chooseData.threeChannelName}];
        this.$set(item,'threeChannelList',threeChannelList);//三级渠道下拉数据


        // if(this.clickIndex==0){
        //   this.customerEnterprise=[{code:chooseData.supplierCode, name: chooseData.supplierName}]  
        //   console.log(this.customerEnterprise)
        //   this.$set(this.billForm,'customerName',chooseData.supplierName)
        //   this.$set(this.billForm,'customerCode',chooseData.supplierCode)
        //   this.$set(this.billForm,'divisionName',chooseData.businessUnitName)
        //   this.$set(this.billForm,'divisionCode',chooseData.businessUnitCode)
        // }
        },
        coreIdhandleClose(){
        this.dialogVisible_coreId=false;
        },
        invoiceInfo(){
            this.dialogVisible_invoiceInfo=true;
        },
        invoiceInfoConfirm(){
          this.dialogVisible_invoiceInfo=false;
        },   
        invoiceInfoCancel(){
          this.dialogVisible_invoiceInfo=false;
        },
        coreIdhandleClose(){
          this.dialogVisible_invoiceInfo=false;
        },
        billFun(){
          this.$router.push({ name: 'customercostbBill'})
        }
    }
  }
  </script>
  
  <style scoped lang="scss">
  .titlecontent {
    position: relative;
  }
    .last_btn {
      span{
        color: red;
        margin-right: 20px;
      }
      float: right;
      margin: 15px 25px 15px 0;
      .upload-excel {
        display: inline-block;
        margin: 0 8px;
      }
      .el-button{
          font-size: 12px;
          width: 50px;
          height: 20px;
          line-height: 20px;
          padding: 0;
          border-radius: 6px;
      }
    }
  .titlecontent .bigtitle {
    font-size: 20px;
    text-align: center;
    margin-top: -40px;
  }
  .ac {
    text-align: center;
  }
  .numberbox {
    position: absolute;
    bottom: -10px;
    right: 20px;
  }
  .closebox {
    position: absolute;
    top: 0px;
    right: 20px;
    font-size: 26px;
    cursor: pointer;
  }
  .title {
    font-size: 16px;
    background: #f2f2f2;
    width: 100%;
    height: 40px;
    line-height: 40px;
    padding-left: 10px;
  }
  .el-button {
    padding: 8px 60px;
    font-size: 12px;
  }
  [class*=' el-icon-'],
  [class^='el-icon-'] {
    font-size: 20px;
    cursor: pointer;
  }
  .el-form-item__content {
    line-height: 30px;
    position: relative;
    font-size: 14px;
    width: 210px !important;
  }
  .headerSearch {
    height: 30px;
    overflow: hidden;
    position: relative;
    /*padding-right: 120px;*/
  }
  /*点开收缩*/
  .info {
    position: relative;
  }
  .flagbox {
    position: absolute;
    right: 20px;
    top: 0;
    background: red;
  }
  .star {
    color: red;
  }
  .el-dialog__header {
    padding: 20px 20px 10px;
    border-bottom: 1px solid #ccc !important;
    margin-bottom: 20px;
  }
  
  /deep/ .el-form--inline .el-form-item__content {
    width: calc(80% - 100px);
    .el-upload {
      display: block;
    }
    input {
      width: 100%;
    }
  }
  .operatorbtn .el-button {
    font-size: 16px;
    width: 100px;
    height: 32px;
    line-height: 32px;
    padding: 0;
    border-radius: 6px;
  }
  .iconRed {
    color: red;
    margin-right: 4px;
  }
  /deep/ .custominfo .el-upload-list__item .el-icon-close {
    display: block;
    position: absolute;
    top: 6px;
    right: 5px;
    cursor: pointer;
    opacity: 0.75;
    color: #fff;
    background: #e0e2e8;
    border-radius: 50%;
  }
  .o_i_btn .el-button {
    border: none;
    text-align: center;
    padding: 3px 27px;
    font-weight: 500;
    height: 20px;
    
  }
  .o_i_btn {
    .upload-excel {
        display: inline-block;
        margin: 0 8px;
      }
  }
  .daornr {
    margin-left: 20px;
  }
  
  .daor input {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
  }
  .daort input {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
  }
  .upload-demo {
    display: inline-block;
  }
  /deep/ td .el-table td,
  /deep/ td .el-table th {
    background: rgb(247, 246, 249) !important;
  }
  
  </style>
  