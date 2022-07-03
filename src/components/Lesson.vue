<script>
export default{
    props: {
        lesson: Object
    },
    methods: {
        addToCart(){
            this.$emit('add-cart', this.lesson)
        }
    }
}

</script>

<template>
    <div class="card">
        <img src="https://mdbcdn.b-cdn.net/img/new/standard/nature/184.webp" class="card-img-top"/>
        <div class="card-body">
            <h5 class="card-title text-black" v-text="lesson.subject"></h5>

            <p class="card-text mb-0" v-text="lesson.location"></p>
            <p class="card-text mb-0">Price: {{lesson.price}}</p>
            <p class="card-text"><small>Available: {{lesson.stock}}</small></p>
            <a href="#!" class="btn btn-primary" v-on:click="addToCart(lesson)">Add to cart</a>
        </div>
    </div>
</template>