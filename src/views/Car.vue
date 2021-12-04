<template>
  <div class="container">
    <div class="card mt-4">
      <div class="card-header">
        Products car
      </div>
      <div class="card-body">
        <template v-if="products.length === 0">
          <p>No existen productos</p>
        </template>
        <template v-if="products.length > 0">
          <table class="table table-hover table-borderless">
            <thead>
              <tr>
                <th scope="col"></th>
                <th scope="col">Name</th>
                <th scope="col">Price</th>
                <th scope="col">Inventory</th>
                <th scope="col">Quantity</th>
                <th scope="col"></th>
              </tr>
            </thead>
            <tbody>
              <template v-for="product in products" :key="product.id">
                  <ProductCar :product="product" :reloadCarFun="reloadCarFun" />
              </template>
            </tbody>
          </table>
          <button @click="buyNow" class="btn btn-warning btn-fill">Buy now</button>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import { get, removeAll } from '../services/car';
import { ref, watchEffect } from 'vue';
import ProductCar from '../components/ProductCar.vue';
import Swal from 'sweetalert2';

export default {
  components: {
    ProductCar
  },
  setup() {
    const products = ref([]);
    const reloadCar = ref(false);

    watchEffect(() => {
      
      reloadCar.value;

      products.value = get();
      

    });

    const reloadCarFun = () => {
      reloadCar.value = !reloadCar.value;
      
    }

    const buyNow = async () => {
    
      // TODO: logica para generar nueva orden
      const exis = products.value.find(c => c.quantity === undefined);
      const exiszero = products.value.find(c => c.quantity === 0);      
      const exisuno = products.value.find(c => c.quantity > 0);      
      var a = [];
      for (var i = 0; i < products.value.length; i++)
               
       a.push({"id": products.value[i].id,
                    "quantity": products.value[i].quantity});
            
  var url = 'http://ecommerce-php-main.test/api/order';
   
      
      if (exis){
      Swal.fire(
        'Oops...!',
        'You must add quantity to all products',
        'error'
      );
     }else{
       if (exiszero){
      Swal.fire(
        'Oops...!',
        'You must add quantity to all products',
        'error'
      );
      }else{
        if (exisuno){
      const b = []; 
      var c = 0;
      for (var j = 0; j < products.value.length; j++)
            b.push(products.value[j].quantity*products.value[j].price);
        
      for (var k = 0; k < b.length; k++)
            c += b[k];
          

             
      Swal.fire({
          title: 'Are you sure?',
          input: 'email',
          text: "The order total is $" + c + ", You wish to purchase?",
          icon: 'warning',
          inputLabel: 'Enter your email address',
          inputPlaceholder: 'Enter your email address',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Yes, buy now!',
          showLoaderOnConfirm: true,
          preConfirm: (login) => {
            return fetch(url, {
                    method: 'POST', 
                    body: JSON.stringify({"email": login,"products":a}), 
                    headers:{
                      'Content-Type': 'application/json'
                    }
                    }).then(res => res.json())
                    .catch(error => console.error('Error:', error))
                    .then(response => console.log('Success:', response));
   
          },
          
        }).then((result) => {
          if (result.isConfirmed) {
            Swal.fire(
              'Created!',
              'Your order has been created.',
              'success'

            )
            removeAll();
            reloadCarFun();
                       
          }
        });
            };
            };
            };

    }


    return {
      products,
      reloadCarFun,
      buyNow
    }

  }
}
</script>

<style scoped>
  .card-header {
    background: #de1822;
    color: white;
    font-size: 16px;
  }
</style>