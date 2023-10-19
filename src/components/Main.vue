<template>
  <div class="wrapper">
    <button class="add-btn" @click="showForm = !showForm">Добавить комментарий</button>
    <add-com :showForm="showForm"></add-com>
    <com-list></com-list>
  </div>
</template>
<script>
  export default{
    data(){
      return {
        showForm: false,
      }
    },
    methods:{
      
    },
    created(){
      
    }
  }
</script>
<style scoped>
.wrapper {
    display: flex;
    justify-content: center;
}
.add-btn{
  padding: 10px;
  margin-left: 10px;
  margin-bottom: 20px;
  max-width: 200px;
  max-height: 50px;
}

@media (max-width:900px) {
  .wrapper {
    flex-direction: column;
  }
}
</style>
