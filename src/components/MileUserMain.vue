<template>
    <div class="ad">
        <el-container>
            <!-- //页头 -->
            <el-header style="width: auto; height: 50px; background: #faa148;padding: 0px;">
                <!-- //搜索框 -->
                <el-input v-model="title" placeholder="请输入内容" style="width: 300px; margin-left: 40%; margin-top: 5px;"></el-input>
                <el-button icon="el-icon-search" circle @click="sosuo()"></el-button>

                <el-dropdown @command="handleCommand" >
                    <span class="el-dropdown-link" style="margin-left: 500px;">{{ name }}<i class="el-icon-arrow-down el-icon--right"></i></span>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item command="a" >个人信息</el-dropdown-item> 
                        <el-dropdown-item command="d" >订单信息</el-dropdown-item> 
                        <el-dropdown-item command="b" >退出</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
            </el-header>


            <!-- //页体 -->
             <el-main style="background-color: #ffffff; width: 100%;height: auto; ">
            <div>
                <!-- 轮播图 -->
                <el-carousel height="413px" direction="vertical" :autoplay="false">
                    <el-carousel-item v-for="item in page" :key=item.id>
                        <img :src=item.imageurl :title="item.title" style="height: 450px; width: 1800px;">
                    </el-carousel-item>
                </el-carousel>
            </div>
            <!-- 全部商品信息 -->
            <div style="height: 400px;">
                <div style="height: 400px; width: 1050px; margin-left: 300px;">
                    <ul style="height: 300px; width: 150px;list-style: none; list-style-type: disc; float: left; margin: 20px 30px;" v-for="item in allShopa" :key="item.id">
                        <li style="display: list-item;">
                            <div @click="tiao(item)">
                                <img :src="item.pmainimage" height="300px" width="210px" :title="item.title">
                            </div>
                            <h6 style="margin-top: -5px; color: #fe7535; ;">￥{{ item.price }} {{ item.pname }}</h6>
                        </li>
                    </ul>
                </div>
            </div>

            </el-main>
            <el-footer  style="height:50px;display: flex;align-items: center;justify-content: center;">
                <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[5, 10, 15, 20]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="zongshu">
                </el-pagination>
            </el-footer>
        </el-container>
    </div>    
    </template>
    
    <script>
     export default{
        name :'MileUserMain',
        data(){
            return{
                page:[],
                allShopa:[],
                title:"",
                name:"",
                currentPage: 1,
                pageSize:5,
                zongshu:-1
            }
        },
        methods:{
            // 轮播图方法
            lunbo(){
                this.$axios
            .get("lunbo/a.action")
            .then(rs=>{
                this.page=rs.data
            })
            .catch()
        },
        // 查询全部商品信息
        allShop(){
            this.$axios
            .get("product/all.action")
            .then(rs=>{
                this.allShopa=rs.data
            })
            .catch()
        },
        //搜索商品信息
        sosuo(){
            this.$axios
            .get("product/selById.action?title="+this.title)
            .then(rs=>{
                console.log(rs);
                this.allShopa=rs.data;
            })
            .catch()
        },
        //退出
        exit(){
            sessionStorage.clear();
            this.$router.push("/MileLogin");            //跳转到登录MileLogin页面。。。。。。。。。。。。。。
        },
        //分页查询刷新
        list(){
            this.$axios
            .get("product/page.action",{params:{currpage:this.currentPage,size:this.pageSize}})
            .then(rs=>{
                console.log(rs);
                this.allShopa = rs.data.data
                this.zongshu=rs.data.size;
            })
            .catch()
        },
        handleSizeChange(val){
        //每页显示条数发生改变则触发此函数handleSizeChange，参数val作用当前新的每页条数
        this.pageSize=val;
        this.list();
      },
      handleCurrentChange(val){
        //当前页码发生改变则触发此函数handleCurrentChange，参数val作用当前新的页码
        this.currentPage=val;
         this.list();
      },
        // 页头 个人信息
        handleCommand(c){
            if(c==='a'){
                if(sessionStorage.getItem("user")!=null){
                    this.$router.push('/MileUser');
                }else{
                    this.$message.error("请先登录");
                    this.$router.push('/MileLogin');
                }
            }
            if(c==='b'){
                this.exit();
            }
            if(c==='d'){
                if(sessionStorage.getItem("user")!=null){
                    this.$router.push('/MileMyOrder');
                }else{
                    this.$message.error("请先登录");
                    this.$router.push('/MileLogin');
                }
            }
        },
        // 跳转至订单信息
        tiao(item){
            if(sessionStorage.getItem("user")!=null){
                this.$router.push({name:'MileOrder',params:item});            //跳转到MileUserMain页面。。。。。。。。。。。。
                
            }else{
                this.$message.error("请先登录");
                this.$router.push('/MileLogin');
            }
        }
        //初始化
        },
        created(){
            this.name=JSON.parse(sessionStorage.getItem('user'));            //获取用户名称。。。。。。。。。。。。。。。。。。。。。。。。
            this.lunbo();
            this.allShop();
            this.list();
        }
     }
    </script>
    
    <style scoped>
    .ad{
        margin: 0px;
    }
    .el-carousel__item h3 {
    color: #475669;
    font-size: 14px;
    opacity: 0.75;
    line-height: 200px;
    margin: 0;
  }
  
  .el-carousel__item:nth-child(2n) {
    background-color: #99a9bf;
  }
  
  .el-carousel__item:nth-child(2n+1) {
    background-color: #d3dce6;
  }
    </style>