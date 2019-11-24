<template>
  <div>
      <Card>
        <p slot="title" class="card-title">
            <Icon type="home"></Icon>
            <span>首页</span>
        </p>
        <div>
          <el-carousel indicator-position="outside">
            <el-carousel-item>
              <img class="demo-img" src="../../assets/images/1.png">
            </el-carousel-item>
            <el-carousel-item>
              <img class="demo-img" src="../../assets/images/2.png">
            </el-carousel-item>
            <el-carousel-item>
              <img class="demo-img" src="../../assets/images/3.png">
            </el-carousel-item>
            <el-carousel-item>
              <img class="demo-img" src="../../assets/images/4.png">
            </el-carousel-item>
            <el-carousel-item>
              <img class="demo-img" src="../../assets/images/5.png">
            </el-carousel-item>
          </el-carousel>
        </div>
      </Card>
  </div>
</template>
<script>
export default {
}
</script>
<style>
</style>

