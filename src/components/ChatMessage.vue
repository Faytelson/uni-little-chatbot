<template>
    <div class="chat-message">
        {{ data.text }}
    </div>
</template>
    
<script>

export default {
    data: function() {
        return {

        }
    },
    props: ['data'],
}

</script>
    
<style>
    .chat-message {
        font-family: 'Roboto', sans-serif;
        font-size: 16px;
        font-weight: 400;
    }
</style>