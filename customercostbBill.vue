<template>
    <div  class="kc_right-main" :class="{'kc_top_unfold_table':headerType}">
        <!--      // 列表-->
        <div  class="kc_top_search_wrap" :class="{'kc_top_search_wrap_unfold':headerType}">
            <!--        表单-->
            <div ref="formId" :class="{'height':headerType}" class="kc_top_search">
                <div  class="searchListstyle">
                    <div class="formcontent">
                        <el-form ref="sizeForm" :model="sizeForm" :inline="true" label-width="120px" size="mini" >
                            <el-form-item label="财务组织">
                                <el-select
                                v-model="sizeForm.orgCode"
                                placeholder="请选择"
                                clearable
                                filterable
                                popper-class="orgCode"
                                >
                                <el-option
                                    :label="item.companyName"
                                    :value="item.company"
                                    :key="index"
                                    v-for="(item, index) in organizationlist"
                                ></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="NC客户费用单单号">
                                <el-input v-model="sizeForm.topBillNo" clearable></el-input>
                            </el-form-item>
                            <el-form-item label="客户">
                                <el-select
                                v-model="sizeForm.customer"
                                placeholder="请选择"
                                clearable
                                filterable
                                @focus="customerFocus"
                                popper-class="customer"
                                >
                                <el-option
                                    :label="item.customerName"
                                    :value="item.customerCode"
                                    :key="index"
                                    v-for="(item, index) in customlist"
                                ></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="单据类型">
                                <el-select
                                v-model="sizeForm.trantypeCode"
                                placeholder="请选择"
                                clearable
                                filterable
                                >
                                <el-option
                                    :label="item.name"
                                    :value="item.code"
                                    :key="index"
                                    v-for="(item, index) in trantypeCodeList"
                                ></el-option>
                                </el-select>
                            </el-form-item> 
                            <el-form-item label="产品线">
                                <el-select
                                v-model="sizeForm.productLineCode"
                                placeholder="请选择"
                                clearable
                                filterable
                                @focus="productLineApi"
                                popper-class="productLine"
                                >
                                <el-option
                                    :label="item.productLineName"
                                    :value="item.productLineCode"
                                    :key="index"
                                    v-for="(item, index) in cpxlist"
                                ></el-option>
                                </el-select>
                            </el-form-item> 
                            <el-form-item label="事业部">
                                <el-select
                                v-model="sizeForm.businessunit"
                                placeholder="请选择"
                                clearable
                                filterable
                                @focus="businessunitFocus"
                                popper-class="businessunit"
                                >
                                <el-option
                                    :label="item.businessUnitName"
                                    :value="item.businessUnitCode"
                                    :key="index"
                                    v-for="(item, index) in businesslist"
                                ></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="费用类型">
                                <el-select
                                v-model="sizeForm.trantypeCode"
                                placeholder="请选择"
                                clearable
                                filterable
                                >
                                <el-option
                                    :label="item.name"
                                    :value="item.code"
                                    :key="index"
                                    v-for="(item, index) in trantypeCodeList"
                                ></el-option>
                                </el-select>
                            </el-form-item> 
                            <el-form-item label="摘要">
                                <el-input v-model="sizeForm.remark" clearable></el-input>
                            </el-form-item>
                            <el-form-item label="核心ID">
                                <el-input v-model="sizeForm.topBillNo" clearable></el-input>
                            </el-form-item>
                            <el-form-item label="三级渠道">
                                <el-select
                                v-model="sizeForm.businessunit"
                                placeholder="请选择"
                                clearable
                                filterable
                                @focus="businessunitFocus"
                                popper-class="businessunit"
                                >
                                <el-option
                                    :label="item.businessUnitName"
                                    :value="item.businessUnitCode"
                                    :key="index"
                                    v-for="(item, index) in businesslist"
                                ></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="单据日期" style="margin-right:120px">
                                <el-date-picker
                                v-model="billDate"
                                type="datetimerange"
                                :default-time="['00:00:00','23:59:59']"
                                @change="dateChange"
                                value-format="yyyy-MM-dd HH:mm:ss"
                                range-separator="至"
                                start-placeholder="开始日期"
                                end-placeholder="结束日期"
                                style="width:320px"
                                >
                                </el-date-picker>
                            </el-form-item>  
                            <el-form-item label="业务日期" style="margin-right:120px">
                                <el-date-picker
                                v-model="billDate"
                                type="datetimerange"
                                :default-time="['00:00:00','23:59:59']"
                                @change="dateChange"
                                value-format="yyyy-MM-dd HH:mm:ss"
                                range-separator="至"
                                start-placeholder="开始日期"
                                end-placeholder="结束日期"
                                style="width:320px"
                                >
                                </el-date-picker>
                            </el-form-item>  
                        </el-form>
                    </div>
                </div>
                <!--        按钮-->
                <div class="kc_top_search_button">
                    <el-button type="primary" @click="searchForm">查询</el-button>
                    <el-button @click="emptyFun" style="margin-right: 10px">清空</el-button>
                    <span v-if="headerType" class="headerico kc_top_search_unfold_btn" style="font-size: 12px" @click="headeIco">
                        收起
                        <i class="el-icon-arrow-up"></i>
                        </span>
                        <span v-else class="headerico" @click="headeIco" style="color:#3D7AFF;font-size: 12px">
                        展开
                        <i class="el-icon-arrow-down"></i>
                    </span>
                </div>
            </div>
        </div>
        <div style="text-align: right; padding: 20px 40px 0 20px" class="opectionBox">
            <el-button type="primary" :disabled="chooseList && chooseList.length<1">确认选择</el-button>
        </div>
        <div class="kc_content_wrap">
            <div class="kc_common_bg">
                <div class="scrollbar table_box kc_content_table">
                    <el-table
                            ref="multipleTable"
                            :data="tableData"
                            tooltip-effect="dark"
                            style="width: 100%"
                            :height="kc_table_height"
                            class="eltable"
                            border
                            stripe
                            v-loading="loading"
                            @selection-change="handleSelectionChange">
                        <div slot="empty" class="kc_table_empty">
                            暂无数据
                        </div>
                        <el-table-column
                                type="selection"
                                fixed
                                width="55">
                        </el-table-column>
                         <el-table-column
                                type="index"
                                label='序号'
                                fixed
                                align='center'
                                width="55">
                        </el-table-column>
                        <el-table-column
                                v-for='(item,index) in customercostList'
                                :key="item.label"
                                :prop="item.value"
                                :min-width='item.width'
                                :label="item.label"
                                :align="item.align?item.align:'center'"
                                show-overflow-tooltip
                        >
                             <template slot-scope="scope">
                                    <span v-if="item.value=='dataFrom'">{{scope.row[item.value] | dataFromFormat}}</span>
                                    <span v-else-if="item.colorfield" :class="(scope.row[item.value] && scope.row[item.value])>=0?'positiveNuColor':'negativeNuColor'" >{{scope.row[item.value] | rbstateFormat}}</span>
                                    <span v-else>{{scope.row[item.value]}}</span>
                             </template>
                        </el-table-column>
                    </el-table>
                </div>
                <Pagination
                        :pageTotal="pagination.pageTotal"
                        :pageIndex="pagination.page"
                        :totalPageNum="pagination.totalPageNum"
                        ref="paging"
                        @handleCurrentChange="handleCurrentChange"
                        @handleSizeChange="handleSizeChange"
                ></Pagination>
            </div>
        </div>
    </div>
</template>
<script>
    import { mapState, mapActions } from "vuex";
    import func from '@/lib/commonfun';
    import Pagination from "@/views/components/pagination";
    import HeadSearchMixins from "@/common/mixins/headSearch";
    import { getOrganization,getProductline,getcusTom,getBusiness,inoutCodeDropDown} from '@/api/basic_info';
    import { cashPlanning,cashPlanningExport } from '@/api/budgetPlan';
    import {  } from '@/api/channelexpenseManage';
    export default {
        name: 'customercostbBill',
        mixins: [HeadSearchMixins],
        components: {
            Pagination
        },
        data(){
            return {
                headerType: false,
                loading: true, 
                sizeForm: {
                    page:0,
                    size:20,
                    orgCode:'',//组织
                },
                tableData: [],
                multipleSelection: [],
                // 分页切换
                pagination: {
                    pageTotal:0,
                    page:1,
                    pageSize:20,
                    totalPageNum:0
                },
                billDate:[],
                organizationlist:[],//财务组织
                cpxlist:[],//产品线
                trantypeCodeList:[{name:'账扣费用',code:1},{name:'票扣费用',code:0}],
                idBrandList:[{name:'是',code:1},{name:'否',code:0}],
                customlist:[],//客户
                uploadText:'导入',
                upload_loading:false,
                businesslist:[],
                datetimerange:[],
                title1:0,
                title2:0,
                title3:0,
                title4:0,
                activeName:'first',
                inoutCodeList:[],//收支项目
                chooseList:[]
            }
        },
        computed: {
            ...mapState({
                customercostList:state => state.channelexpenseManage.customercostList
            })
        },
        filters: {
            // 金额千分位
            rbstateFormat (cellValue) {
                return func.rbstateFormat(cellValue);
            }
        },
        created(){
            getProductline({}).then((res) => {
              this.cpxlist = res.body
            })
        },
        activated(){
            // 更新面包屑
            var breadcrumbList=[{title:"渠道费用账单",url:""},{title:"账扣发票",url:""},{title:"账扣发票维护",url:""},{title:"选择客户费用单(说明：渠道账单中,帐扣费用的收票管理)",url:""}];
            this.$store.commit('updatePublicdata',{key:'breadcrumbList',val:breadcrumbList});
            this.$store.commit('updatePublicdata',{key:'isDropdown',val:true});
            this.getList(this.sizeForm);
        },
        methods:{
            // 请求列表
            getList(sizeForm){
                this.tableData=[];
                this.loading = true;
                for(var key in sizeForm){
                    console.log(sizeForm[key])
                    if(sizeForm[key] === null){
                        sizeForm[key] = '';
                    }
                 }
                cashPlanning(sizeForm).then((res) => {
                    this.loading = false;
                    if(res.code == "0") {
                        let tableData=res.data.content;
                        this.tableData=tableData;
                        setTimeout(()=>{
                            this.$refs.multipleTable.doLayout();    
                        },100)
                        this.pagination.pageTotal=res.data.totalElements;
                        this.pagination.totalPageNum=res.data.totalPages;
                        console.log(this.pagination.pageSize)
                    }else{
                        func.notifyFun(this,'提示','warning',res.message)
                    }
                },(err)=>{
                    this.loading = false;
                    func.notifyFun(this,'提示','warning', err.response.data.message)
                })
            },
            // 选中的表格行
            handleSelectionChange(list) {
                  let chooseList=[];
                  list.map((item)=>{
                    chooseList.push(item.toBillHeaderId);
                  })
                  this.chooseList=chooseList;
            },
            handleSizeChange(val){
                this.pagination.pageSize = val;
                this.$refs.paging.paginations.pageSize=val;
                this.sizeForm.size=val;
                this.sizeForm.page=this.pagination.page-1;
                this.getList(this.sizeForm);
            },
            // 点击分页  页码
            handleCurrentChange(val){
                this.pagination.page = val;
                this.sizeForm.size=this.pagination.pageSize;
                this.sizeForm.page=val-1;
                this.getList(this.sizeForm);
            },
            // 查询
            searchForm(){
                this.sizeForm.page=0;
                this.pagination.page=1;
                this.getList(this.sizeForm);
            },
             // 清空
            emptyFun() {
                Object.keys(this.sizeForm).forEach((key) => {
                    if (key != 'page' && key != 'size') {
                       this.sizeForm[key] = '';
                    }
                })
                this.billDate=[];
            }, 
           dateChange(vm) {
               console.log(vm);
                if(vm){
                    this.sizeForm.brandHkOverDateEnd = vm[1]
                    this.sizeForm.brandHkOverDateStart = vm[0]
                }else{
                    this.sizeForm.brandHkOverDateEnd = ''
                    this.sizeForm.brandHkOverDateStart = ''
                }
            },
             // 获取财务组织
             orgCodeFocus() {
                if (this.organizationlist.length < 1) {
                    func.createLoading('.orgCode', this)
                    func.requestApi(getOrganization, '', 'organizationlist', this)
                }
            },
            orgCodeChange(e) {
                let obj = {}
                obj = this.organizationlist.find((item) => {
                    return item.company === e
                })
                this.sizeForm.orgCode = obj == undefined ? '' : obj.company
                this.sizeForm.orgName = obj == undefined ? '' : obj.companyName
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
             // 获取事业部getcusTom
            businessunitFocus() {
                if (this.businesslist.length < 1) {
                    func.createLoading('.businessunit', this)
                    var data = { businessUnitCode: '', businessUnitName: '' }
                    func.requestApi(getBusiness, data, 'businesslist', this)
                }
            },
            // 产品线
            productLineApi(index) {
                if (this.cpxlist.length < 1) {
                    func.createLoading('.productLine', this)
                    getProductline({}).then((res) => {
                        this.loadingselect.close()
                        this.cpxlist = res.body
                    })
                }
            },
            inoutCodeApi(){
                if (this.inoutCodeList.length < 1) {
                    func.createLoading('.inoutCode', this)
                    func.requestApi2(inoutCodeDropDown, '', 'inoutCodeList', this)
                }
            },
            exportBind(){
               this.$confirm('请确定导出吗?','提示',{
                confirmButtonText:'确定',
                cancelButtonText:'取消',
                type:'warning'
              }).then((res)=>{
                let useInfo=JSON.parse(sessionStorage.getItem('logoninfo'));
                let params = {
                    ...this.sizeForm,
                    toEmail:useInfo.email
                };
                cashPlanningExport(params).then((res)=>{
                  if(res.code == 0){
                      func.notifyFun(this,'提示','success',res.data);
                      this.getList(this.sizeForm);
                  }else{
                      func.notifyFun(this,'提示','warning',res.data);
                  }
                },(err)=>{
                    func.notifyFun(this,'提示','warning', err.response.data.message)
                })
              }).catch((res)=>{})
            },
            handleClick(tab, event) { 

            },
            addBind(){
                this.$router.push({path:'/zkinvoiceAdd'})
            }

        }
    }
</script>

<style scoped>
  .kc_content_wrap {
        height: calc(100% - 135px);
   }
    .kc_top_unfold_table .kc_content_wrap {
        height: calc(100% - 158px);
    }
.opectionBox{
  text-align: right;
  margin-right:30px;
}
.opectionBox div{
    display: inline-block;
}
.bg-purple-dark {
     background: #d3dce6;
  }
.grid-content-jiesuan {
    border-radius: 4px;
    min-height: 36px;
    margin-top: 10px;
    margin-left: 20px;
    margin-right: 20px;
    margin-bottom: -10px;
    line-height: 36px;
}
/deep/ .form-month .el-form-item__content{
   width:600px !important;
}
.form-month .el-date-editor--month{
  display: inline-block !important;
}
</style>


