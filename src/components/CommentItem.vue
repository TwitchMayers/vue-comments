<template>
    <div :class="['comment', reactionClass]" :style="{ 'margin-left': `${comment.level * 20}px` }">
        <span class="comment__author">
            {{ comment.author }}
        </span>
        <p class="comment__text">
            {{ comment.text }}
        </p>
        <div class="comment__info">
            <button @click="showForm = !showForm">Ответить</button>
            <div>
                <span class="comment__reply" v-if="responsesCount">
                    <img class="comment__reply-img" src="../assets/reply.svg" alt="">
                    <span>{{responsesCount}}</span>
                </span>
                <span>{{ comment.createdAt }}</span>
            </div>
        </div>
    </div>
    <add-com :showForm="showForm" :id="comment.id" :level="comment.level"></add-com>
</template>

<script>
export default {
    name: 'com-item',
    props: {
        comment: {
            type: Object,
            required: true
        },
        responsesCount: Number,
        reactionSum: Number
    },
    computed: {
        reactionClass() {
            if (this.reactionSum < 0) {
                return 'negative';
            } else if (this.reactionSum > 0) {
                return 'positive';
            } else {
                return 'neutral';
            }
        }
    },
    data(){
      return {
        showForm: false,
      }
    },
}
</script>

<style scoped>
.comment {
    border: 1px solid #fafafa;
    max-width: 400px;
    padding: 20px 10px;
    border-radius: 10px;
    margin: 10px 0;
}

.comment__text{
    word-wrap: break-word;
}
.comment__author {
    font-weight: bold;
}

.comment__info {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-top: 20px;
}

.comment__reply{
    margin-right: 10px;
}

.comment__reply-img{
    width: 16px;
}

.negative {
    background-color: #7F1D1D;
}

.positive {
    background-color: #14532D;
}
</style>