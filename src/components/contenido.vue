<template>

  <section class="contenido">
      <b-container fluid>
      <b-row class="p-3">
        <b-col lg="8">
            <b-form-input
            v-model="tituloNota"
            @keyup.enter="anadirNota"
            placeholder="Introduce texto nota..."
            >
            </b-form-input>
        </b-col>
        <b-col 
        class="mt-2 mt-lg-0"
        lg="4">
            <b-form-input
            v-model = "empiezaPor"
            placeholder="Introduce texto nota a buscar..."
            >
            </b-form-input>
        </b-col>
      </b-row>
      <b-row class="ml-3">
        <div>
          <p> {{totalPendientes}} tareas pendiendes de un total de {{totalTareas}} |</p>
        </div>
        <div>
          <p @click="borrarTareasCompletadas" class="tareasCompletas ml-1">Borrar tareas completadas</p>
        </div>
      </b-row>
      <b-row>
        <b-col 
        offset-lg="2"
        lg="8">
        
          <nota 
      v-for = "nota in listaRecordatoriosFiltrada"
      :key = "nota.fecha.toString()"
      :nota="nota"
      @borrarNota ="borrarNotaLista"
      @editarNota ="cambiarNombre"
      @cambiarPrioridad ="cambiarPrioridad"
      @tareaCompletada ="tareaCompletada"
      />
        </b-col>
      </b-row> 
    </b-container>
  </section>

</template>

<script lang="js">
  import nota from "./nota.vue";

  export default  {
    name: 'contenido',
    components: {
      nota
    },
    props: [],
    mounted () {
     if(localStorage.listaNotas)
      this.listaNotas = JSON.parse(localStorage.listaNotas); 
    },
    data () {
      return {
        listaNotas : [],
        tituloNota:"",
        empiezaPor:""
      }
    },
    methods: {
      anadirNota:function(){
        this.listaNotas.push({
          titulo: this.tituloNota,
          prioridad:0,
          fecha: new Date(),
          completado:false
        });
        this.tituloNota="";
        this.actualizarLocalStorage();
      },
      actualizarLocalStorage: function(){
        localStorage.listaNotas = JSON.stringify(this.listaNotas);
      },
      borrarNotaLista : function(fecha){
        let posicion = this.listaNotas.findIndex(nota => nota.fecha == fecha);
        this.listaNotas.splice(posicion, 1);
        this.actualizarLocalStorage();
      },
      cambiarNombre: function(fecha,tituloNotaEditada){
        let posicion = this.listaNotas.findIndex(nota => nota.fecha == fecha);
        this.listaNotas[posicion].titulo = tituloNotaEditada;
        this.actualizarLocalStorage();
      },
      cambiarPrioridad:function (fecha,tituloPrioridad) {
        let posicion = this.listaNotas.findIndex(nota => nota.fecha == fecha);
        this.listaNotas[posicion].prioridad = tituloPrioridad;
        this.actualizarLocalStorage();
      },
      tareaCompletada:function (fecha,completada) {
        let posicion = this.listaNotas.findIndex(nota => nota.fecha == fecha);
        this.listaNotas[posicion].completado = completada;
        this.actualizarLocalStorage();
      },
      borrarTareasCompletadas: function()
    {
      this.listaNotas = this.listaNotas.filter((nota)=>{
        return !nota.completado;
      })
      this.actualizarLocalStorage();    
    },
    },
    computed: {
      listaRecordatoriosFiltrada: function()
    {
      let listaFiltrada;
      if(this.empiezaPor == "")
        listaFiltrada =  this.listaNotas;
      else
        listaFiltrada = this.listaNotas.filter((recordatorio)=>
        {
          if(recordatorio.titulo.indexOf(this.empiezaPor) >= 0 )
            return true;
          else
            return false;
        });
      return listaFiltrada;
    },
    totalPendientes: function()
    {
      let total = 0;
        
      for(let i=0;i<this.listaNotas.length;i++)
        if(!this.listaNotas[i].completado)
          total++;

      return total;
    },
      totalTareas: function()
    {
      return this.listaNotas.length;
    }
    }
}


</script>

<style scoped>
  .contenido {
    min-height: 80%;
    background-color: black;
  }

  p{
    color: #f1f1f1;
  }

  .tareasCompletas{
    color: orange;
    cursor: pointer;
  }
  input{
    background-color: #f1f1f1;
  }
</style>
