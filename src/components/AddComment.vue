<template>
    <form v-show="showForm" class="add-form" @submit.prevent="addComment" :style="{ 'margin-left': `${level * 20 +20}px`}">
        <label class="add-form__label">
            <span>Имя</span>
            <input class="add-form__name" type="text" v-model="author" placeholder="Имя" required>
        </label>
        <label class="add-form__label">
            <span>Комментарий</span>
            <input class="add-form__comment" type="text" v-model="text" placeholder="Добавьте ваш комментарий здесь" required>
        </label>
        <div class="reactions">
            <input class="reactions__input" type="radio" :id="`like-${id}`" name="reaction" value="1" v-model="reaction">
            <label :for="`like-${id}`"><img class="reactions__img" src="../assets/like.svg" alt=""/></label>
            <input class="reactions__input" type="radio" :id="`default-${id}`" name="reaction" value="0" v-model="reaction" checked>
            <label :for="`default-${id}`"><img class="reactions__img" src="../assets/default.svg" alt=""/></label>
            <input class="reactions__input" type="radio" :id="`dislike-${id}`" name="reaction" value="-1" v-model="reaction">
            <label :for="`dislike-${id}`"><img class="reactions__img" src="../assets/dislike.svg" alt=""/></label>
        </div>

      <button :disabled="isLoading" :class="{ 'negative': isLoading }">Добавить</button>
  </form>
</template>
<script>
    import axios from 'axios';

    export default{
    name: 'add-com',
    props: ['showForm','id','level'],
    data(){
        return{
        author: '', 
        text: '',
        reaction: 0,
        parentId: this.id?this.id:null,
        isLoading: false
        }
    },
    methods:{
        addComment(){
            this.isLoading = true;
            const data = {
                author: this.author, 
                text: this.text,
                reaction: this.reaction,
                parentId: this.parentId,
            };

            axios.post('http://194.67.93.117/comments', data,{
                headers: {
                    'Username': 'Sh1mpi'
                }
            })
            .then(response => {
                console.log(response.data);
                this.isLoading = false
            })
            .catch(error => {
                console.log(error);
                this.isLoading = false
            });
        }
    },
    created(){
        
    }
    }
</script>
<style scoped>
.add-form{
    max-width: 400px;
    max-height: 250px;
    padding: 10px;
    border: 1px solid #fafafa;
    border-radius: 10px;
}
.add-form__label{
    display: flex;
    flex-direction: column;
}

.add-form__name{
    max-width: 150px;
}
.add-form__comment{
    padding-bottom: 30px;

}
.reactions{
    margin: 10px 0;
}

.reactions__img {
    width: 32px;
    margin-right: 10px;
}

.reactions__input {
    display: none;
}

input[type="radio"]:checked + label .reactions__img {
    background-color: #fafafa;
}

.negative {
    background-color: #FECACA;
}

</style>