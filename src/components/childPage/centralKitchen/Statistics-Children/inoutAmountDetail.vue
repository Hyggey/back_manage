<!--进销存明细-->
<template>
    <div>
        <!--头部搜索-->
        <div class="searchWrap">
            <div class="search-top" style="display:flex;">
                <div class="item">
                    <span style="font-size:18px;font-weight:600;float:left;">总部仓库</span>
                    <el-select style="width:290px;margin-right:20px;float:left;"  v-model="name_in1" placeholder="请选择">
                        <el-option label="产成品仓" :value="1"></el-option>
                        <el-option label="低值易耗品仓" :value="2"></el-option>
                        <el-option label="百货用品仓" :value="3"></el-option>
                        <el-option label="原材料仓" :value="4"></el-option>
                    </el-select>
                    <span style="font-size:18px;font-weight:600;float:left;">日期</span>
                    <el-date-picker
                    size="large"
                    style="margin-right:20px;width:180px;"
                    v-model="value1"
                    type="date"
                    placeholder="选择日期">
                    </el-date-picker>
                    <!--  -->
                    <el-date-picker
                    v-model="value2"
                    type="date"
                    style="width:180px;"
                    placeholder="选择日期">
                    </el-date-picker>
                    <!--  -->
                    <el-button type="primary" size="small" icon="el-icon-search" @click="seachAndClear(1)" style="margin-left:10px;">搜索</el-button>
                    <el-button type="info"  size="small" icon="el-icon-refresh" @click="seachAndClear(2)">重置</el-button>
                </div>
            </div>
            <div class="search-bottom" style="margin-top:20px;display:flex;">
                <!--  -->
                <div  class="item">
                    <span style="font-size:18px;font-weight:600;float:left;">物品类别</span>
                    <!--组件-->
                    <div class="fl">
                    	<storeSelect :storeSelectData='storeSelectData' style="margin-right:20px;"></storeSelect>
                    </div>
                    <span style="font-size:18px;font-weight:600;float:left;">物品名称</span>
                    <el-input style="margin-right:60px;width:310px;float:left;" size="medium" clearable></el-input>
                </div>
            </div>
        </div>
        <!--分割线-->
        <hr>
        <!--导出-->
        <div>
            <el-button type="primary" icon="el-icon-upload" @click="Export" style="margin-bottom:20px;">导出</el-button>
        </div>
        <!--点出导出对话框-->
        <el-dialog
            :visible.sync="centerDialogVisible"
            width="30%"
            top="5vh"
            center>
            <span slot="title" class="dialog-title" style="display:flex;justify-content:flex-start;font-size:16px;color:#333;">
               没有需要导出的数据
            </span>
            <span slot="body" class="dialog-body" style="display:none;">
            </span>
            <span slot="footer" class="dialog-footer"  style="display:flex;justify-content:flex-end;">
                <el-button type="primary" @click="centerDialogVisible = false">确 定</el-button>
            </span>
        </el-dialog>
         <!--数据-->
        <div class="table_Parcel">
        	<div class="table_Content">
        		<table border="0" cellspacing="0" > 
	                <tr>
	                    <th style="width:30px;"></th>
	                    <th colspan="10"></th>
	                    <th colspan="2">入库</th>
	                    <th colspan="2">出库</th>
	                    <th colspan="2">结存</th>
	                </tr>
	                <tr class="addline">
	                    <th style="width:30px;"></th>
	                    <th class="ui-jqgrid-sortable">仓库</th>
	                    <th class="ui-jqgrid-sortable">物品类别</th>
	                    <th class="jqgh_grid-table_goodsCode">物品编码</th>
	                    <th class="ui-jqgrid-sortable">物品名称</th>
	                    <th class="ui-jqgrid-sortable">规格型号</th>
	                    <th class="jqgh_grid-table_goodsCode">库存单位</th>
	                    <th class="jqgh_grid-table_goodsCode">业务类型</th>
	                    <th class="jqgh_grid-table_goodsCode">日期</th>
	                    <th class="ui-jqgrid-sortable">供应商</th>
	                    <th style="width:145px;">单据号</th>
	                    <th class="jqgh_grid-table_qty">数量</th>
	                    <th class="jqgh_grid-table_qty">金额</th>
	                    <th class="jqgh_grid-table_qty">数量</th>
	                    <th class="jqgh_grid-table_qty">金额</th>
	                    <th class="jqgh_grid-table_qty">数量</th>
	                    <th class="jqgh_grid-table_qty">金额</th>
	                </tr>
	                <!-- <tr v-for="(v,k) in data_list" :key="k">
	                    <td>{{k+1}}</td>
	                    <td>{{v.s_name}}</td>
	                    <td>{{v.brand}}</td>
	                    <td>{{v.areaName}}</td>
	                    <td>{{v.attribute}}</td>
	                    <td>{{v.formats}}</td>
	                    <td>{{v.user}}</td>
	                    <td>{{v.time}}</td>
	                    <td>{{v.state}}</td>
	                    <td>{{v.template}}</td>
	                    <td>{{v.time1}}</td>
	                    <td>{{v.time2}}</td>
	                    <td>{{v.time3}}</td>
	                </tr> -->
	                <!-- 无数据显示 -->
               
                </table>
                <div class="ui-jqgrid-bdiv" style="width:100%;height:100px;"></div>
        	</div>
        </div>
        <!-- 分页 -->
        <div class="pageBtn">
            <el-pagination
            :background="true"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="pageIndex"
            :page-sizes="[10, 20, 30, 40, 50]"
            :page-size="pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="9990">
            </el-pagination>
        </div>
    </div>
</template>
<script>
export default {
    data(){
        return{
            value1:'',//时间选择器1
            value2:'',//时间选择器2
            name_in1:'',
            name_in2:'',
            name_in3:'',
            pageIndex:1,//当前页数
            pageSize:10,//每页显示条数
            centerDialogVisible: false,//对话框
            storeSelectData:[//组件传值
                {
                    name:'所有物品分类',
                    id:'1',
                    children:[
                        {name:'开业物品',id:'1-1'},
                        {name:'常用物品',id:'1-2'},
                        {name:'冷冻产成品',id:'1-3'},
                        {name:'印字低值易耗品',id:'1-4'},
                        {name:'产成品',id:'1-5'},
                        {name:'低值易耗品',id:'1-6'},
                        {name:'门店原料品',id:'1-7'},
                    ]
                },
            ],
            data_list:[ //渲染数据
                // {s_name:'',brand:'',attribute:'',areaName:'',formats:'',user:'',time:'',state:'',template:'',time1:'',time2:'',time3:'',},
            ],
        }
    },
    methods:{
        //点出导出事件
        Export(){
            this.centerDialogVisible=true;
        },
        // pageSize 改变时会触发
        handleSizeChange(data){
            console.log(data)
            this.pageSize=data
        },
        // pageIndex改变事件
        handleCurrentChange(data){
            console.log(data)
            this.pageIndex=data
        },
    },
    components:{
    }
}
</script>
<style scoped>
    /*头部搜索样式*/
    hr{
        width:100%;
        margin:10px auto;
        border: 0;
        height: 1px;
        background:#eee;
    }
    .searchWrap .item{
        height:50px;
        line-height:50px;
    }
    .searchWrap .item span{
        display:inline-block;
        width:100px;
        text-align:right;
        margin-right:20px;
    }
    .pageBtn{
        margin: 20px auto 0 25%;
    }
    .addline th{
        border-top:1px solid #ccc;
    }
    .table_Parcel{
    	width:100%;
    }
    .table_Content{
    	overflow-x:auto;
        width:100%;
    }
    table{
    	width:auto;
        width: 2060px;
    }
    table .addline th.ui-jqgrid-sortable{
    	width:195px;
    }
    table .addline th.jqgh_grid-table_goodsCode{
    	width:95px;
    }
    table .addline th.jqgh_grid-table_qty{
    	width:75px;
    }
    table th{
        height:40px;
        border: 1px solid #e1e1e1!important;
    }
    table td{
        border: 1px solid #e1e1e1!important;
    }
</style>