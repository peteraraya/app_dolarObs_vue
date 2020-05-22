<template>

<div>

    <v-layout :wrap="true">
      <v-flex xs12>
          <v-card>
              <v-date-picker 
                v-model="fecha" 
                full-width
                locale="es-cl"
                :min="minimo"
                :max="maximo"
                @change="getDolar(fecha)"
                >
  
              </v-date-picker>
          </v-card>
        <v-card color="error" dark>
            <v-card-text class="display-1 text-center ">
                   Dolar Observado :  <br>${{valor}} <small>pesos chilenos</small>
            </v-card-text>
        </v-card>


      </v-flex>
    </v-layout>

</div>

</template>

<script>
// Importamos axios
import axios from 'axios';
import { mapMutations } from 'vuex'

export default {
    data(){
      return{
        fecha:  new Date().toISOString().substr(0,10),
        minimo: '1984',
        maximo: new Date().toISOString().substr(0,10),
        valor: null 
      }
    },
    methods:{

      ...mapMutations(['mostrarLoading','ocultarLoading']),

      // await va indicar que esto finalize para continuar con nuestro codigo
      async getDolar(dia){
        console.log(dia);
        // dar vuelta la fecha
        let arrayFecha = dia.split(['-'])
        console.log(arrayFecha)

        let ddmmyy = arrayFecha[2]+'-'+arrayFecha[1]+'-'+arrayFecha[0];

        console.log(ddmmyy)

        try{
            this.mostrarLoading({titulo:'Accediendo a información', color:'secondary'})
            let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)

          if(datos.data.serie.length > 0 ){
            this.valor = await datos.data.serie[0].valor
          }else{
            this.valor = 'Sin resultados';
            console.log(this.valor);
          }

        }catch (error) {
          console.log(error)
        }
        finally{
            this.ocultarLoading();
        }



      }
    },
    created(){
      this.getDolar(this.fecha)
    }
}
</script>
