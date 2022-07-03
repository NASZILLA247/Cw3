<script>
import Lesson from './components/Lesson.vue'
import Checkout from './components/Checkout.vue'

export default{
  components: {
    Lesson, Checkout
  },
  data() {
    return {
        searchValue: '',
        showProduct: true,
        flow: 'Ascending',
        lessons: [],
        cart: [],
        sortType: '',
        firstName: "",
        lastName: "",
        phoneNumber: ""
    }
  },
  methods: {
        getLessons: function () {
            fetch("https://nasiru-cst.herokuapp.com/collection/lessons")
                .then(res => {
                    return res.json()
                })
                .then(data => {
                    this.lessons = data
                    console.log(data)
                })

                .catch(err => {
                    lessons = "unable to get lessons data"
                    console.log("unable to get lessons list")
                })
        },
        showCheckout() {
            this.showProduct = this.showProduct ? false : true;
        },
        addToCart(lesson) {
            if (lesson.stock >= 1) {
                let InCart = false
                if (this.cartCount() >= 1) {
                    for (let i = 0; i < this.cart.length; i++) {
                        if (this.cart[i].id == lesson._id) {
                            this.cart[i].stock += 1
                            InCart = true
                            break
                        }
                    }
                    if (InCart == false) {
                        let item = {}
                        item.id = lesson._id
                        item.stock = 1
                        this.cart.push(item)
                    }
                }
                else {
                    let item = {}
                    item.id = lesson._id
                    item.stock = 1
                    this.cart.push(item)
                }
                lesson.stock -= 1
            }
            else {
                lesson.stock = 0
            }
            console.log(this.cart)
        },
        removeItem(id) {
            let showcart = this.cart
            let less = this.lessons
            for (let i = 0; i < showcart.length; i++) {
                console.log(showcart[i].id)
                if (id == showcart[i].id) {
                    showcart.splice(i, 1)

                }
            }
            for (let i = 0; i < less.length; i++) {
                console.log(less[i].id)
                if (id == less[i].id) {
                    less[i].stock += 1

                }
            }
        },
        checkPage() {
            let checkout = []
            for (let i = 0; i < this.cart.length; i++) {
                for (let k = 0; k < this.lessons.length; k++) {
                    if (this.lessons[k]._id == this.cart[i].id) {
                        let item = {}
                        item.id = this.cart[i].id
                        item.subject = this.lessons[k].subject
                        item.location = this.lessons[k].location
                        item.price = this.lessons[k].price
                        item.images = this.lessons[k].images
                        item.stock = this.cart[i].stock
                        checkout.push(item)
                    }
                }
            }
            return checkout
        },
        checkOut(orders){
            let order = {
                name: orders.firstName +' '+orders.lastName,
                phone_number: orders.phoneNumber,
                items: this.cart
            }
            let order_string = (JSON.stringify(order))
            fetch('https://nasiru-cst.herokuapp.com/collection/orders', {
                method: "POST",
                body: order_string,
                headers: {
                    "Content-type": "application/json; charset=UTF-8"
                }
            })
            .then(response => response.json())
            .then(json_response => {
                console.log(json_response)
                this.orderClose(orders)
            })
            .catch(err => console.log(err))
        },
        orderClose(orders){
            let stockNew = []
            let showcart = this.cart
            
            for (let i = 0; i < showcart.length; i++) {
                for (let j = 0; j < this.lessons.length; j++) {
                    if (showcart[i].id == this.lessons[j]._id) {
                        let item = {
                            id: showcart[i].id,
                            stock: this.lessons[j].stock
                        }
                        stockNew.push(item)
                    }
                }
            }
            let stockString = (JSON.stringify(stockNew))
            fetch('https://nasiru-cst.herokuapp.com/collection/lessons', {
                method: "PUT",
                body: stockString,
                headers: {
                    "Content-type": "application/json; charset=UTF-8"
                }
            })
                .then(res => res.json())
                .then(res => {
                    console.log(res)
                    alert('Order Successfully')
                    
                    showcart.splice(0, showcart.length)
                })
                .catch(err => console.log(err))
            if (orders.firstName == '' && orders.lastName == '' && orders.phoneNumber == '' && this.cart.length == 0) {
                alert('Error')
            }
        },
        sortPrice: function (arr) {
            function compare(a, b) {
                if (a.price > b.price)
                    return 1;
                if (a.price < b.price)
                    return -1;
                return 0;
            }
            return arr.sort(compare);
        },
        sortSubject: function (arr) {
            function compare(a, b) {
                if (a.subject > b.subject)
                    return 1;
                if (a.subject < b.subject)
                    return -1;
                return 0;
            }
            return arr.sort(compare);
        },
        sortLocation: function (arr) {
            function compare(a, b) {
                if (a.location > b.location)
                    return 1;
                if (a.location < b.location)
                    return -1;
                return 0;
            }
            return arr.sort(compare);
        },
        sortAvailable: function (arr) {
            function compare(a, b) {
                if (a.stock > b.stock)
                    return 1;
                if (a.stock < b.stock)
                    return -1;
                return 0;
            }
            return arr.sort(compare);
        },
        sortedArray: function() {
            let allLessons = this.lessons
         
            if (this.sortType == 'price') {
                allLessons = this.sortPrice(allLessons)
            }
            else if (this.sortType == 'subject') {
                allLessons = this.sortSubject(allLessons)
            }
            else if (this.sortType == 'location') {
                allLessons = this.sortLocation(allLessons)
            }
            else if (this.sortType == 'available') {
                allLessons = this.sortAvailable(allLessons)
            }
            if (this.flow == 'ascending') {
                return allLessons
            }
            else if (this.flow == 'descending') {
                return allLessons.reverse()
            }
            return allLessons
        },
        cartCount() {
            return this.cart.length
        }
    },
    computed: {
        cartItemNum: function () {
            return this.cart.length
        },
    },
    created() {
        this.getLessons()
    }
}
</script>

<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <!-- Container wrapper -->
      <div class="container">

          <!-- Toggle button -->
          <button class="navbar-toggler" type="button" data-mdb-toggle="collapse"
              data-mdb-target="#navbarButtonsExample" aria-controls="navbarButtonsExample" aria-expanded="false"
              aria-label="Toggle navigation">
              <i class="fas fa-bars"></i>
          </button>

          <!-- Collapsible wrapper -->
          <div class="collapse navbar-collapse" id="navbarButtonsExample">
              <!-- Left links -->
              <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                  <li class="nav-item">
                      <a class="nav-link" href="#">CST 3145</a>
                  </li>
              </ul>
              <!-- Left links -->

              <div class="d-flex align-items-center">
                  
                  <a class="position-relative me-3" v-on:click="showCheckout">
                      <i class="fas fa-shopping-cart fs-4"></i>
                      <span class="position-absolute top-0 start-100 translate-middle badge rounded-pill bg-danger">
                          {{cartItemNum}}
                        </span>
                  </a>
              </div>
          </div>
          <!-- Collapsible wrapper -->
      </div>
      <!-- Container wrapper -->
  </nav>
  <div class="container">
    <div v-if="showProduct">
        <div class="form-a">
            <div class="row mt-5">
                <div class="col-md-6 mb-2">
                    <div class="form-group">
                        <input type="text" v-model="searchValue"
                            class="form-control p-2 ps-3 rounded-0 shadow-0 form-control-lg form-control-a"
                            placeholder="Search">
                    </div>
                </div>
                <div class="col-md-3 mb-2">
                    <div class="form-group">
                        <select name="sortType" v-model="sortType" v-on:change="sortedArray()"
                            class="form-control p-2 bg-white ps-3 form-select rounded-0 shadow-0 form-control-a form-control-lg">
                            <option value="">--Sort By--</option>
                            <option value="price">Price</option>
                            <option value="location">Location</option>
                            <option value="available">Avalibility</option>
                            <option value="subject">Subject</option>
                        </select>
                    </div>
                </div>
                <div class="col-md-3 mb-2">
                    <div class="form-group">
                        <select name="flow" v-model="flow" v-on:change="sortedArray()" class="form-control p-2 bg-white ps-3 form-select rounded-0 shadow-0 form-control-a form-control-lg">
                            
                            <option value="ascending">Ascending</option>
                            <option value="descending">Descending</option>
                        </select>
                    </div>
                </div>

            </div>
        </div>

        <div class="row pt-4">
            <div v-for="lesson in lessons" :key="lesson" class="col-lg-3 mb-3">
                <Lesson :lesson="lesson" v-on:add-cart="addToCart($event)"></Lesson>
            </div>
        </div>
    </div>

    <div v-else>
        <Checkout :cart="checkPage()" v-on:rem-lesson="removeItem($event)" v-on:checkout="checkOut($event)"></Checkout>
    </div>
            
 </div>
</template>

<style>

</style>