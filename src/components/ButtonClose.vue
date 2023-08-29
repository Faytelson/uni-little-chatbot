<template>
    <div class="button-close">
        <button 
            class="button-close__btn"
            @click="emitEvent"
        ></button>
    </div>
</template>

<script>
export default {
    data() {
        return {

        }
    },
    methods: {
        emitEvent() {
            this.$emit('emitClose')
        }
    }
}
</script>

<style>
.button-close {
    display: flex;
}

.button-close__btn {
    width: 26px;
    height: 26px;
    background: url('../assets/images/close.png') center/contain no-repeat;
    border: 1px solid #fff;
    border-radius: 50%;
    transition: all .2s linear;
    cursor: pointer;
}

.button-close__btn:hover {
    transform: rotate(180deg);
}
</style>