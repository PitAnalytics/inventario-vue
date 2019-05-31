<template app>
  <v-app dark>

      <!--GRUPO DE BUSQUEDA-->
      <v-layout v-show="search.visible">
        <v-flex xs12>
          <v-container>
            <v-card>

              <v-layout>
                <v-flex>
                  <v-card-title>
                    <h4 class="headline mb-0" color="blue lighten-4">INTRODUCIR PARAMETROS DE BUSQUEDA</h4>
                  </v-card-title>
                </v-flex>
              </v-layout>

              <v-layout>

                <v-flex xs12>

                      <v-layout justify-space-between row>

                        <v-flex xs12>
                          <v-container>
                            <v-select 
                              v-model="search.zona.selected"
                              :items="search.zona.list"
                              label="ZONA"
                              v-on:change="zonaChange()"
                            ></v-select>
                          </v-container>
                        </v-flex>

                        <v-flex xs12>
                          <v-container>
                            <v-select
                              v-model="search.equipo.selected"
                              :items="search.equipo.list"
                              label="CODIGO EQUIPO"
                              v-on:change="equipoChange()"
                            ></v-select>
                          </v-container>
                        </v-flex>

                      </v-layout>

                      <v-layout row justify-center>
                        <v-flex xs6>
                          <v-container>
                            <v-card-text>{{search.equipo.option.descripcion}}</v-card-text>
                          </v-container>
                        </v-flex>

                        <v-flex xs6 >
                          <v-container>
                            <v-img v-bind:src="search.equipo.option.img" width="130px" height="130px" class="image">
                            </v-img>
                          </v-container>
                        </v-flex>

                      </v-layout>
                </v-flex>

              </v-layout>

            </v-card>
          </v-container>
        </v-flex>
      </v-layout>


      <!--GRUPO DE TABLA PRINCIPAL EN BUSCADOR-->
      <v-layout v-show="table.visible">
        <v-flex xs12>
          <v-container>
            <v-card id="tableInventario" >
              <v-container v-if="table.isset">

                <v-layout>
                  <v-flex>
                      <v-card-title>
                        <h4 class="headline mb-0">INVENTARIO</h4>
                      </v-card-title>
                  </v-flex>
                </v-layout>

                <v-layout>
                  <v-flex xs12>
                    <v-data-table
                        hide-actions 
                        :items="table.inventario.list"
                        class="elevation-1"
                      >
                      <template v-slot:items="props">
                        <tr v-bind:id="props.item.id" v-on:click="inventarioClick(props.item.id)" row>
                          <td class="text-xs-left">{{props.item.id}}</td>
                          <td class="text-xs-left">{{props.item.zona}}</td>
                          <td class="text-xs-left">{{props.item.codigoEquipo}}</td>
                          <td v-if="props.item.usando" class="text-xs-left"><v-icon color="green accent-2" medium>check_circle</v-icon></td>
                          <td v-else class="text-xs-left"><v-icon color="red accent-3" medium>cancel</v-icon></td>
                        </tr>
                      </template>
                    </v-data-table>
                  </v-flex>
                </v-layout>

              </v-container>
            </v-card>
          </v-container>
        </v-flex>
      </v-layout>

      <!--FORMULARIO DE EDICION-->
      <v-layout id="formUpdate" v-show="update.active">
        <v-flex xs12>
          <v-container>
            <v-card>
              <v-container>

                <!--titulo-->
                <v-layout>
                  <v-flex>
                    <v-container>
                      <v-card-title>
                        <h4 class="headline mb-0">ACTUALIZAR</h4>
                      </v-card-title>
                    </v-container>
                  </v-flex>
                </v-layout>

                <!--fomulario-->
                <v-layout>
                  <v-flex xs12>
                    <v-container>

                      <v-layout>

                        <v-flex xs6>
                          <v-container>
                            <v-text-field
                              v-bind:value="update.option.id"
                              label="Regular"
                              disabled
                            ></v-text-field>                          
                          </v-container>
                        </v-flex>

                        <v-flex xs6>
                          <v-container>
                            <v-select
                            :items="update.zonaList"
                            v-model="update.option.zona"
                            label="ZONA">
                            </v-select>
                          </v-container>
                        </v-flex>

                      </v-layout>

                      <v-layout>

                        <v-flex xs6>
                          <v-container>
                            <v-btn color="green accent-2" medium dark v-if="update.option.usando" v-model="update.option.usando" v-on:click="usandoClick()">
                              USANDO
                              <v-icon>check</v-icon>
                            </v-btn>
                              <v-btn color="red accent-3" medium dark v-if="!update.option.usando" v-model="update.option.usando" v-on:click="usandoClick()">
                              USANDO
                              <v-icon>clear</v-icon>
                            </v-btn>
                          </v-container>
                        </v-flex>

                        <v-flex xs6>
                          <v-container>
                            <v-btn
                            v-on:click="inventarioUpdate()"
                            color="blue lighten-4"
                            medium
                            >ACTUALIZAR</v-btn>
                          </v-container>
                        </v-flex>

                      </v-layout>

                    </v-container>
                  </v-flex>
                </v-layout>

              </v-container>
            </v-card>
          </v-container>
        </v-flex>
      </v-layout>
  </v-app>
</template>

<script>

import axios from 'axios';

export default {

  name: 'App',
  components: {
  },
  data () {
    return {

      service:{
        url:'http://10.133.8.193/inventario-back/public/'
      },

      search:{
        visible:true,
        zona:{
          list:[],
          selected:'*'
        },

        equipo:{
          list:[],
          option:{
            codigo:'*',
            descripcion:'--------------------',
            img:'https://heuft.com/upload/image/400x267/no_image_placeholder.png'
          },
          selected:'*'
        }
      },

      table:{
        visible:true,
        isset:false,
        inventario:{
            list:null,
            headers:["#","ZONA","CODIGO","EN USO"]
        }
      },

      update:{
        zonaList:[],
        active:false,
        option:{
          id:0,
          zona:'CDMX',
          usando:false
        }
      }


    }
  },
  mounted() {

    this.load();


  },
  methods: {


    //AXIOS
    async searchTableIndex(){

      let inventarios = await axios.get(this.service.url+'inventario');
      this.table.inventario.list=inventarios.data;
      this.table.isset=true;

    },
    async searchZonaIndex(){

      let zonas = await axios.get(this.service.url+'zona');
      this.search.zona.list.push('*');
      zonas.data.forEach(zona => {
        this.search.zona.list.push(zona.zona);
      });

    },
    async searchEquipoIndex(){

      let equipos = await axios.get(this.service.url+'equipo');
      this.search.equipo.list.push('*');
      equipos.data.forEach(equipo => {
        this.search.equipo.list.push(equipo.codigo);
      });

    },
    async searchEquipoGet(codigo){
      if(codigo!=="*"){
        let equipos = await axios.get(this.service.url+'equipo/obtener/'+codigo);
          this.search.equipo.option=equipos.data;
      }
      else{
        this.search.equipo.option = {
            codigo:'*',
            descripcion:'--------------------',
            img:'https://heuft.com/upload/image/400x267/no_image_placeholder.png'
          }
      }
    },
    async searchInventarioIndex(zona,codigo){

      let inventarios = await axios.get(this.service.url+'inventario/busqueda/'+zona+'/'+codigo);
      this.table.inventario.list=inventarios.data;

    },

    async searchInventarioGet(id){

      let inventario = await axios.get(this.service.url+'inventario/obtener/'+id);
      this.update.option=inventario.data;

    },

    async searchInventarioUpdate(id,zona,usando){

      let usandoString='';
      if(usando){
        usandoString='1';
      }
      else{
        usandoString='0';
      }

      let inventario = await axios.get(this.service.url+'inventario/actualizar/'+id+'/'+zona+'/'+usandoString);

    },

    //FUNCION DE PRIMERA CARGA  
    load(){
      this.searchTableIndex();
      this.searchZonaIndex();
      this.searchEquipoIndex();
    },

    //FUNCIONES DE FORMULARIOS Y OBJETOS
    equipoChange(){
      this.searchEquipoGet(this.search.equipo.selected);
      this.searchInventarioIndex(this.search.zona.selected,this.search.equipo.selected);
    },

    zonaChange(){
      this.searchInventarioIndex(this.search.zona.selected,this.search.equipo.selected);
    },
    inventarioClick(id){
      this.searchInventarioGet(id);
      this.search.zona.list.forEach(zona=>{
        this.update.zonaList.push(zona);
      });
      this.update.zonaList.shift();
      this.update.active=true;
      this.search.visible=false;   
      this.table.visible=false;
      this.toolbar.visible=false;
    },
    inventarioUpdate(){
      if(this.update.active){
        this.searchInventarioUpdate(this.update.option.id,this.update.option.zona,this.update.option.usando);
        this.update.active=false;
        this.search.visible=true;   
        this.table.visible=true;
        this.toolbar.visible=true;
        //this.search.equipo.selected='*';
        this.search.zona.selected='*';
        this.searchTableIndex();
      }
    },
    usandoClick(){
      if(this.update.option.usando){
        this.update.option.usando=false;

      }
      else{
        this.update.option.usando=true;
      }
    }

  }
}
</script>
