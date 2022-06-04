<template>
    <div class="container">
        <input id="input" 
            @keyup.enter="add" 
            placeholder="Enter your task... and press ENTER" 
            v-model="input"
            @input="limit"
            :maxlength="maxInput"
        >
        <h3>{{maxInput - input.length}}</h3>
        <img @click="add" src="add.svg" />
    </div>
</template>

<script>
    export default {
        data: () => ({input: "", maxInput: 255}),
        methods: {
            add() {
               return this.input && (this.$emit('add', this.input), this.input = '')
            }
        }
    }
</script>

<style scoped>
h3 {
    padding: 5px;
    font-weight: normal;
    opacity: 0.7;
}
img {
    width: 25px;
    padding: 5px;
    cursor: pointer;
    opacity: 0.85;
}
img:hover {
    opacity: 1;
}
.container {
    width: calc(100% - 20px);
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    padding: 10px;
}
#input {
    width: 300px;
    padding: 5px;
}
</style>