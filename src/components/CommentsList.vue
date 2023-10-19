<template>
        <label class="stream">
            <input type="checkbox" v-model="liveUpdates">
            <span>Live updates</span>
        </label>
        <div class="comments">
            <com-item 
                v-for="comment in comments" 
                :key="comment.id" 
                :comment="comment"
                :responsesCount="comment.children ? comment.children.length : 0"
                :reactionSum="computeReactionsSum(comment)"
            />
        </div>
</template>

<script>
import { EventSourcePolyfill } from 'event-source-polyfill';
export default {
    name: 'com-list',
    data() {
        return {
            comments: [],
            liveUpdates: false,
        }
    },
    methods: {
        computeReactionsSum(comment) {
        let sum = 0;
        if (comment.children) {
            for (let child of comment.children) {
                sum += child.reaction;
            }
        }
        return sum;
    }
    },
    async created() {
        const response = await fetch('http://194.67.93.117/comments');
        if (!response.ok) {
            console.error('HTTP error', response.status);
            return;
        }
        const data = await response.json();
        // основные комментарии
        function buildTree(comments, parentId = null,level=0) {
            let tree = [];
            
            comments.forEach(comment => {

                let dateStr = comment.createdAt
                let dateObj = new Date(dateStr);

                let year = dateObj.getFullYear();
                let month = String(dateObj.getMonth() + 1).padStart(2, '0');
                let day = String(dateObj.getDate()).padStart(2, '0');
                let hours = String(dateObj.getHours()).padStart(2, '0');
                let minutes = String(dateObj.getMinutes()).padStart(2, '0');

                let formattedDate = `${year}-${month}-${day} ${hours}:${minutes}`; 
                comment.createdAt = formattedDate


                if (comment.parentId === parentId) {
                    comment.level = level
                    let children = buildTree(comments, comment.id,level+1);
                    if (children.length) {
                        comment.children = children;
                    }
                    tree.push(comment);
                }
            });

            return tree;
        }

        // проход по дереву
        function flattenTree(tree) {
            let list = [];

            tree.forEach(node => {
                list.push(node);
                if (node.children) {
                    list.push(...flattenTree(node.children));
                    console.log(node.children);
                }
            });

            return list;
        }
        let tree = buildTree(data.sort((a, b) => a.id - b.id));
        this.comments = flattenTree(tree);
        
        const eventSource = new EventSourcePolyfill('http://194.67.93.117/comments/stream', {
            headers: {
                'Accept': 'text/event-stream',
                'Username': 'Sh1mpi'
            }
        });
        
        eventSource.onmessage = (event) => {
            const newComment = JSON.parse(event.data);
            if (this.liveUpdates) {
                data.push(newComment);
                let tree = buildTree(data.sort((a, b) => a.id - b.id));
                this.comments = flattenTree(tree);
            }
        };
    }
}
</script>

<style scoped>

.comments{
    padding: 0 10px;
}

.stream {
    margin-left: 10px;
}
</style>