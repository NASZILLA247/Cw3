<script>
export default{
    props: {
        cart: Array
    },
    data(){
        return {
            orders: {
                firstName: "",
                lastName: "",
                phoneNumber: ""
            }
        }
    },
    methods: {
        checkOut(){
            this.$emit('checkout', this.orders)
        },
        removeItem(){
            this.$emit('rem-lesson', {id: id, stock: stock})
        },
    }
}

</script>

<template>
    <div class="row">
        <div class="col-lg-12">
            <div class="col-6">
                <div class="mt-5">
                    <div class="row mb-4">
                        <div class="col">
                        <div class="form-outline">
                            <label class="form-label" for="form6Example1">First name</label>
                            <input type="text" id="form6Example1"  v-model="orders.firstName" class="form-control" required/>
                        </div>
                        </div>
                        <div class="col">
                        <div class="form-outline">
                            <label class="form-label" for="form6Example2">Last name</label>
                            <input type="text" id="form6Example2"  v-model="orders.lastName" class="form-control" required/>
                            
                        </div>
                        </div>
                    </div>
                    <!-- Number input -->
                    <div class="form-outline mb-4">
                        <label class="form-label" for="form6Example6">Phone</label>
                        <input type="number" id="form6Example6"  v-model="orders.phoneNumber" class="form-control" required/>
                    </div>
                    
                    
                    
                    <!-- Submit button -->
                    <button class="btn btn-primary btn-block mb-4" v-on:click="checkOut()">Place order</button>
                </div>
            </div>
        </div>
        <div v-for="lesson in cart" :key="lesson" class="col-3 mb-3">
            <div class="card">
                <img src="https://mdbcdn.b-cdn.net/img/new/standard/nature/184.webp" class="card-img-top"/>
                <div class="card-body">
                    <h5 class="card-title text-black" v-text="lesson.subject"></h5>

                    <p class="card-text mb-0" v-text="lesson.location"></p>
                    <p class="card-text mb-0">Price: {{lesson.price}}</p>
                    <a href="#!" class="btn btn-danger" v-on:click="removeItem(lesson.id)">Remove</a>
                </div>
                </div>
        </div>
                    
    </div>
                

</template>