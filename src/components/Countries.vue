<template>
  <div> 
  <div class="row">
     <div class="col-ms-12">
        <div class="col-md-4">
        <p>Introduzca algún carácter del país que quiere buscar</p>
            <input type="text" class="form-control" v-model="buscador" placeholder="Buscar paises...">
            <div class="row margintop10">
                <ul v-for="pais in paises_encontrados">
                    <li><a @click="setPais(pais.alpha3_code)" href="#">{{pais.name}}</a></li>
                </ul>
            </div>            
        </div>
        <div class="col-md-7">
            <div class="row">      
                <p>Seleccione un país para ver los estados que componen ese país (Ej. India, United States, etc.)</p>
                    <select class="form-control" v-model="pais">
                        <option value="">Seleccionar país</option>
                        <option v-for="pais in paises" :value="pais.alpha3_code">{{pais.name}}</option>                
                    </select> 
            </div>
            <div class="row margintop10" v-if="tieneEstados">
                <div class="panel panel-primary" v-for="estado in estados">
                    <div class="panel-heading">
                        <h3 class="panel-title">{{estado.name}} <span>( {{estado.abbr}} )</span></h3>
                    </div>
                    <div class="panel-body">
                        <h3>Capital: {{estado.capital}}</h3>
                        <h4>Ciudad más grande: {{estado.largest_city}}</h4>
                    </div>
                </div>        
            </div>
            <div class="row" v-else>
                <h4>No hay información para el país seleccionado</h4>
            </div>        
        </div>
     </div>
  </div>  
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'countries',
  mounted () {
      this.cargarPaises();  
  },
  data () {
    return {
      paises: [],
      paises_encontrados: [],
      estados: [],
      pais: '',
      buscador: null
    }
  },
  methods: {
      cargarPaises(){
        axios.get('http://services.groupkt.com/country/get/all')
        .then((response)=>{
            this.paises= response.data.RestResponse.result;
            },
        error=>alert(error)); 
      },
      cargarEstados(codigo_pais){
        const url=`http://services.groupkt.com/state/get/${codigo_pais}/all`;
        axios.get(url)
        .then((response)=>{
            //console.log(response);
            this.estados= response.data.RestResponse.result;
            },
        error=>alert(error));           
      },
      buscarPais(criterio){
        const url=`http://services.groupkt.com/country/search?text=${criterio}`;
        axios.get(url)
        .then((response)=>{
            //console.log(response);
            this.paises_encontrados= response.data.RestResponse.result;
            },
        error=>alert(error));           
      },
      setPais(codigo){
        this.pais=codigo;
      }
  },
  watch: {
    pais: function (codigo) {
        //console.log(pais);
        this.cargarEstados(codigo);
    },
    buscador: function(criterio){
        //console.log(criterio);        
        if (criterio){
            this.buscarPais(criterio);
        } 
        else{
            this.paises_encontrados=[];
        }       
    }      
  },
  computed: {
      tieneEstados(){
          return this.estados.length>0
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  /*margin: 0 5px;*/
}

a {
  color: #337ab7;
  font-weight: bold;
  text-align:left;
}
.aright{
    text-align:right;
}

.margintop10{
    margin-top:10px;
}
</style>
