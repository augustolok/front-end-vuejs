<template>
  <div id="app">

    <nav>
      <div class="nav-wrapper blue darken-1">
        <a href="#" class="brand-logo center">prova vuejs com laravel</a>
      </div>
    </nav>

    <div class="container">
      <div id="errors">  
      </div>
      <form @submit.prevent="salvar">
         
          <label>Valor</label>
          <input type="text" placeholder="Valor" v-model="produto.saldo">
          <br>
          <label>Tipo da Movimentação:</label>
          <div class="form-check">
            <input class="form-check-input" type="radio" v-model="produto.status" name="status" id="flexRadioDefault1" value="-">
            <label class="form-check-label" for="flexRadioDefault1">
              saque
            </label>
          </div>
          <div class="form-check">
            <input class="form-check-input" type="radio" v-model="produto.status" name="status" id="flexRadioDefault2" checked value="+">
            <label class="form-check-label" for="flexRadioDefault2">
              deposito
            </label>
          </div>
          <br>
          <button class="btn btn-primary">Salvar<i class="material-icons left">save</i></button>

      </form>

      <table class="table">

        <thead>

          <tr>
            <th>#</th>
            <th></th>
            <th>VALOR</th>
          </tr>

        </thead>

        <tbody>
          <tr v-for =" produto of produtos" :key="produto.id">
            <td v-if="produto.status == '-'" class="table-danger">{{produto.id}}</td>
            <td v-else class="table-success">{{produto.id}}</td>
            <td v-if="produto.status == '-'" class="table-danger"></td>
            <td v-else class="table-success"></td>
            <td v-if="produto.status == '-'" class="table-danger"> <span style="color:red"  >R${{produto.status}}{{formatPrice (produto.saldo)}}</span></td>
            <td v-else class="table-success"><span style="color:green" >R${{produto.status}}{{formatPrice (produto.saldo)}}</span></td>
           
            

          </tr>
         
          <tr>
           <tr class="table-info">
             <td colspan="2">Tota</td>
            <td>R${{formatPrice (this.totals)}}</td>
          </tr>

        </tbody>
      
      </table>
      
    </div>
    
  </div>
</template>

<script>

import axios from 'axios'



export default {
    data () {
        return {
          produto : {
            saldo : ''
          },
          totals: [],
          errors: null,
            produtos: []
        }
    },
    created () {
       this.listar()
           
    },
     methods: {
        listar() {
              axios.get('http://127.0.0.1:8000/saldo')
                .then((response) => {
                   
                    let somar = 0 ;
                    // Prints "Jean-Luc Picard" followed by "Captain"
                    Object.values(response.data).forEach(val => {
                        if (val.status == '+') {
                          somar = somar + val.saldo
                        }
                        if (val.status == '-') {
                          somar = somar - val.saldo
                        }
                        this.totals = somar 
                        
                    });
                    this.produtos = response.data                    
            })
        },
        salvar(){
        // POST request using axios with error handling
          axios.post("http://127.0.0.1:8000/store", {saldo:this.produto.saldo,status:this.produto.status }).then(resp => {
            console.log("foi"+resp);
            
            this.listar()
             document.getElementById('errors').innerHTML = `<div class="alert alert-success alert-dismissible fade show" role="alert">
                                                                    <strong>Sucesso! Transação realizada!</strong>
                                                                    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
                                                                      <span aria-hidden="true">&times;</span>
                                                                    </button>
                                                                  </div>`;
                  window.setTimeout(function() {
                      document.getElementById('errors').innerHTML = '';
                  }, 4000);
          })
          .catch(error => {
                  this.errors = error.response.data
                  document.getElementById('errors').innerHTML = `<div class="alert alert-danger alert-dismissible fade show" role="alert">
                                                                    <strong>Erro! `+error.response.data.error+`</strong> .
                                                                    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
                                                                      <span aria-hidden="true">&times;</span>
                                                                    </button>
                                                                  </div>`;
                  
                  window.setTimeout(function() {
                      this.errors = " "
                      document.getElementById('errors').innerHTML = '';
                       
                  }, 4000);
          });     
        }, 
        formatPrice(value) {
            let val = (value/1).toFixed(2).replace('.', ',')
            return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
        }
     }

     
    
    
}
 

</script>


