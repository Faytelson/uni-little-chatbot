<template>
    <button 
        class="button-option"
        @click="emitEvent"
    >
        {{ data.text }}
    </button>
</template>
    
<script>
export default {
    data: function() {
        return {

        }
    },
    props: ['data'],
    mounted() {
        this.$emit('mounted')
    },
    methods: {
        emitEvent() {
            if(this.data.id !== 3) {
                this.$emit('click', { text:this.data.text, answer: this.data.answer, id: this.data.id })
            } else {
                this.$emit('back')
            }
        }
    }
}

</script>
    
<style>
    .button-option {
        font-family: 'Roboto', sans-serif;
        font-size: 16px;
        font-weight: 400;
        width: 100%;
        height: 100%;
        background-color: transparent;
        color: #fff;
        background-color: #dc0c53;
        padding: 0 15px;
        opacity: 0;
    }
</style>