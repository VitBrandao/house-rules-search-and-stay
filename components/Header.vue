<template>
    <div>
        <p><a href="/"> HOUSE RULES </a></p>
        <p><a href="/create"> CREATE NEW RULE </a></p>
        <p><a href="/requests"> TESTING REQUESTS </a></p>
    </div>
</template>

<script>
export default {
    name: 'Header',
}
</script>

<style scoped>
    div {
        list-style: none;
        display: flex;
        background: black;
        justify-content: space-evenly;
        align-items: center;
    }

    p {
        margin-right: 10px;
    }

    a {
        color: #DDD;
        text-decoration: none;
        transition: .5s;
    }

    a:hover {
        color: turquoise;
    }
</style>