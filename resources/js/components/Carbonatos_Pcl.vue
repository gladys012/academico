<template>
    <main class="main">
        <!-- Breadcrumb -->
        <ol class="breadcrumb">
            <li class="breadcrumb-item"><a href="/">Escritorio</a></li>
        </ol>
        <div class="container-fluid">
            <!--Tabla Listado -->
            <div class="card">
                <div class="card-header">
                    <h3>Determinación de Carbonatos en producto de Carbonato de litio</h3>                        
                    <b-button variant="info  pull-right" @click="abrirModal('carbonatos_pcl','registrar')" style="right:0;"><b-icon-plus font-scale="1.5"></b-icon-plus>Nuevo</b-button>
                </div>
                <div class="card-body"> 
                    <b-row>                        
                    <b-col lg="6" class="my-1">
                        <b-form-group
                        label="Filtrar"
                        label-cols-sm="3"
                        label-align-sm="right"
                        label-size="md"
                        label-for="filterInput"
                        class="mb-0"
                        >
                        <b-input-group size="md">
                            <b-form-input
                            v-model="filter"
                            type="search"
                            id="filterInput"
                            placeholder="Introduce la búsqueda"
                            ></b-form-input>
                            <b-input-group-append>
                            <b-button :disabled="!filter" @click="filter = ''" variant="info">Buscar</b-button>
                            </b-input-group-append>
                        </b-input-group>
                        </b-form-group>
                    </b-col> 
                    <b-col sm="4" md="6" class="my-1">
                        <b-form-group
                        label="Mostrar registros"
                        label-cols-sm="6"
                        label-cols-md="4"
                        label-cols-lg="3"
                        label-align-sm="right"
                        label-size="md"
                        label-for="perPageSelect"
                        class="mb-0"
                        >
                        <b-form-select
                            v-model="perPage"
                            id="perPageSelect"
                            size="md"
                            :options="pageOptions"
                        ></b-form-select>
                        </b-form-group>
                    </b-col>                   
                    </b-row>                                                                              
                    <b-table
                        show-empty striped
                        hover responsive 
                        stacked="md"
                        :items="arrayCarbonatos_pcl"
                        :fields="columnas"
                        :current-page="currentPage"
                        :per-page="perPage"
                        :filter="filter"
                        :filterIncludedFields="filterOn"
                        :sort-by.sync="sortBy"
                        :sort-desc.sync="sortDesc"
                        :sort-direction="sortDirection"                     
                    >
                        <template v-slot:head(fecha)="data">
                            <span class="text-primary">{{ data.label }}</span>
                        </template>  
                        <template v-slot:head(codigo_lab)="data">
                            <span class="text-primary">{{ data.label }}</span>
                        </template>  
                        <template v-slot:head(masa_recipiente)="data">
                            <span class="text-primary">{{ data.label }}</span>
                        </template>  
                        <template v-slot:head(masa_muestra)="data">
                            <span class="text-primary">{{ data.label }}</span>
                        </template> 
                        <template v-slot:head(cod_balanza)="data">
                            <span class="text-primary">{{ data.label }}</span>
                        </template>                           
                        <template v-slot:head(acciones)="data">
                            <span class="text-primary">{{ data.label }}</span>
                        </template>                         
                        <template v-slot:cell(acciones)="row">
                        <a href="#" @click="abrirModal('carbonatos_pcl','actualizar',row.item)" v-b-tooltip.hover title="Actualizar">
                            <b-icon-pencil-square variant="info" font-scale="1.5"></b-icon-pencil-square>
                        </a>
                        <a href="#" @click="row.toggleDetails" v-b-tooltip.hover.d50 title="Detalles">
                            <b-icon-eye-fill variant="info" font-scale="1.5"></b-icon-eye-fill >
                        </a>                                        
                        </template>
                        <template v-slot:row-details="row">
                            <b-card>
                                <ul>
                                <li v-for="(value, key) in row.item" :key="key">{{ key }}: {{ value }}</li>
                                </ul>
                            </b-card>
                        </template>
                    </b-table>
                    <b-row>
                    <b-col sm="7" md="6" class="my-1">
                        <b-pagination
                        v-model="currentPage"
                        :total-rows="totalRows"
                        :per-page="perPage"
                        align="fill"
                        size="sm"
                        class="my-0"
                        ></b-pagination>
                    </b-col>
                    </b-row>
                </div>
            </div>
            <!-- Fin -->
        </div>
        <!--Inicio del modal agregar/ modificar-->
        <div class="modal fade" :class="{'mostrarr' : modal}" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
         <div class="modal-dialog modal-lg" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h4 class="modal-title" v-text="tituloModal"></h4>
                    <button type="button" class="close" @click="cerrarModal()" aria-label="Close">
                        <span aria-hidden="true">×</span>
                    </button>
                </div>
                <div class="modal-body">
                    <b-form @submit="onSubmit" v-if="show">
                       <div class="row">
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-1"  label="Fecha:" label-for="input-2" >
                                <datepicker 
                                    :bootstrap-styling="true"
                                    :language="es"                                     
                                    calendar-class="datepicker1"                            
                                    :open-date="openDate"
                                    format="dd/MM/yyyy"
                                    v-model="fechaD"  
                                    calendar-button
                                    calendar-button-icon="fa fa-calendar"   
                                ></datepicker>
                            </b-form-group>
                         </div>
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-2"  label="Código laboratorio:" label-for="input-2" >                                                              
                            <b-form-select v-model="idpreparacion" class="mb-3" required>
                                <b-form-select-option value="0" disabled>-- Seleccionar --</b-form-select-option>
                                <b-form-select-option v-for="preparacion in arrayPreparacion" :key="preparacion.id" :value="preparacion.id" v-text="preparacion.codigo_lab"></b-form-select-option>
                            </b-form-select> 
                            </b-form-group>
                         </div> 
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-3"  label="Masa recipiente:" label-for="input-2" >
                                <b-form-input id="input-3" v-model="masa_recipiente" placeholder="Masa recipiente" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div>                                                
                       </div>                    
                       <div class="row">                                                      
                          <div class="form-group col-sm-4">
                            <b-form-group id="input-group-4"  label="Masa muestra:" label-for="input-4" >
                                <b-form-input id="input-4" v-model="masa_muestra" placeholder="Masa muestra" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div> 
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-5"  label="Cod. balanza:" label-for="input-5" >
                                <b-form-input id="input-5" v-model="cod_balanza" placeholder=" Cod. balanza" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div>
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-3"  label="Vol. gastado:" label-for="input-2" >
                                <b-form-input id="input-3" v-model="vol_gastado_hci" placeholder="Vol. gastado" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div>                                                                            
                       </div>
                       <div class="row">                                                     
                          <div class="form-group col-sm-4">
                            <b-form-group id="input-group-4"  label="Cod. bureta:" label-for="input-4" >
                                <b-form-input id="input-4" v-model="cod_bureta" placeholder="Cod. bureta" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div> 
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-5"  label="Conc. HCI:" label-for="input-5" >
                                <b-form-input id="input-5" v-model="conc_hci" placeholder=" Conc. HCI" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div> 
                         <div class="form-group col-sm-4">
                            <b-form-group id="input-group-5"  label="Dato:" label-for="input-5" >
                                <b-form-input id="input-5" v-model="dato" placeholder=" Dato" required                            
                                ></b-form-input>
                            </b-form-group>
                         </div>                                                                           
                       </div>
                        <div class="form-group row">                                                                
                        <div class="col-md-2">
                            <div class="container">
                                <div class="box">
                                    <div class="checkbox-group">
                                    <input id="checkboxName1" type="checkbox" v-model="cumple"/>
                                    <label for="checkboxName1" class="ta">Cumple</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        </div> 
                       <div class="row">
                          <div class="form-group col-sm-12">
                              <b-form-group id="input-group-6" label="Observaciones:" label-for="input-6">                                
                                <b-form-textarea
                                    id="textarea"
                                    v-model="observaciones"
                                    placeholder="Observaciones"
                                    rows="3" max-rows="4"
                                    ></b-form-textarea>
                            </b-form-group>
                          </div>
                       </div>                           
                      <div class="modal-footer">
                        <b-button type="reset" variant="secondary" @click="cerrarModal()">Cerrar</b-button>
                        <b-button type="submit" v-if="tipoAccion==1" variant="primary" @click="registrarCarbonatos_pcl()">Guardar</b-button>
                        <b-button type="submit" v-if="tipoAccion==2" variant="primary" @click="actualizarCarbonatos_pcl()">Actualizar</b-button>
                      </div>
                    </b-form>                   
                </div>                
            </div>
            <!-- /.modal-content -->
        </div>
        <!-- /.modal-d -->
    </div>
    <!--Fin del modal-->
  </main>
</template>

<script>
    import Datepicker from 'vuejs-datepicker';
    import { es } from 'vuejs-datepicker/dist/locale';
    import moment from "moment";
    export default {
        data (){            
            return {
                es: es,
                openDate: new Date(),

                carbonatos_pcl_id: 0,
                idpreparacion : 0,
                masa_recipiente:'',
                fechaD: new Date(),
                codigo_lab:'',
                cod_balanza : '',
                conc_hci : '',
                observaciones: '',
                vol_gastado_hci:'',
                masa_muestra:'',
                cod_bureta:'',
                arrayCarbonatos_pcl: [],
                arrayPreparacion :[],
                tituloModal : '',
                dato:'',
                cumple:'',

                modal : 0,                
                tipoAccion : 0,
                dismissSecs: 5,
                dismissCountDown: 0,
                show: true,
                columnas: [
                  { key: 'fecha', label: 'Fecha', sortable: true, sortDirection: 'desc' },
                  { key: 'codigo_lab', label: 'Cod. laboratorio', sortable: true, class: 'text-justify' },                 
                  { key: 'masa_recipiente', label: 'Masa recipiente', sortable: true },                 
                  { key: 'masa_muestra', label: 'Masa muestra', sortable: true },                 
                  { key: 'cod_balanza', label: 'Cod. balanza', sortable: true },                 
                  { key: 'acciones', label: 'Acciones',  sortable: false}
                ],
                perPage: 10,
                selectedID: null,
                totalRows: 1,
                currentPage: 1,
                pageOptions: [10, 15, 20,50,100],
                sortBy: '',
                sortDesc: false,
                sortDirection: 'asc',
                filter: null,
                filterOn: [],
            }
        },
        components: {
            Datepicker
          },
        computed:{

        },
        methods : {

            onFiltered(filteredItems) {
              // Trigger pagination to update the number of buttons/pages due to filtering
              this.totalRows = filteredItems.length;
              this.currentPage = 1
            },

            listarCarbonatos_pcl (){
                let me=this;
                var url= '/carbonatos_pcl';
                axios.get(url).then(function (response) {
                    var respuesta= response.data;
                    me.arrayCarbonatos_pcl = respuesta.carbonatos_pcl.data;
                    me.totalRows = me.arrayCarbonatos_pcl.length;
                    console.log(me.arrayCarbonatos_pcl,'me.arrayCarbonatos_pcl...');
                })
                .catch(function (error) {
                    console.log(error);
                });
            },          

            onSubmit(evt) {
                evt.preventDefault()  //val. form
            },
            registrarCarbonatos_pcl(){ 
                if (this.cumple!='') {
                    this.cumple=0;
                }
                console.log(this.fechaD,'entra fecha');
                this.fechaD = moment(this.fechaD).format('D/MM/YYYY');
                console.log(this.fechaD,'fechaaaaaa');
                let me = this;
                    console.log('entra 0');
                        axios.post('/carbonatos_pcl/registrar',{
                        'idpreparacion': this.idpreparacion,    
                        'masa_recipiente': this.masa_recipiente,
                        'fecha': this.fechaD,
                        'codigo_lab' : this.codigo_lab,
                        'masa_muestra' : this.masa_muestra,
                        'cod_balanza' : this.cod_balanza,
                        'vol_gastado_hci' : this.vol_gastado_hci,
                        'cod_bureta' : this.cod_bureta,
                        'conc_hci' : this.conc_hci,
                        'dato' : this.dato,
                        'cumple' : this.cumple,
                        'observaciones' : this.observaciones,
                        'usr_id':1,

                        }).then(function (response) {                   
                        me.listarCarbonatos_pcl();
                        me.cerrarModal();   
                    }).catch(function (error) {
                        console.log(error);
                    });              
            },
            
            actualizarCarbonatos_pcl(){   
                if (this.cumple=='' || this.cumple==false) {
                    this.cumple='';
                }         
                let me = this;
                this.fechaD = moment(this.fechaD).format('D/MM/YYYY');
                axios.put('/carbonatos_pcl/actualizar',{
                    'idpreparacion': this.idpreparacion, 
                    'masa_recipiente': this.masa_recipiente,
                    'fecha': this.fechaD,
                    'codigo_lab' : this.codigo_lab,
                    'masa_muestra' : this.masa_muestra,
                    'cod_balanza' : this.cod_balanza,
                    'vol_gastado_hci' : this.vol_gastado_hci,
                    'cod_bureta' : this.cod_bureta,
                    'conc_hci' : this.conc_hci,
                    'dato' : this.dato,
                    'cumple' : this.cumple,
                    'observaciones' : this.observaciones,
                    'id': this.carbonatos_pcl_id,
                    'usr_id':1,
                }).then(function (response) {
                    me.listarCarbonatos_pcl();
                    me.cerrarModal();
                }).catch(function (error) {
                    console.log(error);
                });               
            },
            
            selectPreparacion(){
                let me=this;
                var url= '/preparacion/selectPreparacion';
                axios.get(url).then(function (response) {
                    console.log(response,'select');
                    var respuesta= response.data;
                    me.arrayPreparacion = respuesta.preparaciones.data;
                    console.log(me.arrayPreparacion,'select 3');
                })
                .catch(function (error) {
                    console.log(error);
                });
            },

            cerrarModal(){
                this.modal=0;
                this.tituloModal='';
                this.idpreparacion= 0;
                this.fechaD = new Date(),
                this.codigo_lab = '';
                this.masa_recipiente = '';
                this.masa_muestra = '';
                this.cod_bureta = '';
                this.observaciones = '';
                this.vol_gastado_hci= '';
                this.cod_balanza= '';
                this.conc_hci= '';
                this.dato='';
                this.cumple='';
            },
            abrirModal(modelo, accion, data = []){
                switch(modelo){
                    case "carbonatos_pcl":
                    {
                        switch(accion){
                            case 'registrar':
                            {
                                this.modal = 1;
                                this.tituloModal = 'Registrar Sulfatos Turbimetría y Espc.';
                                this.idpersona= 0;
                                this.fechaD = new Date(),
                                this.codigo_lab = '';
                                this.masa_recipiente = '';
                                this.masa_muestra = '';
                                this.cod_bureta = '';
                                this.observaciones = '';
                                this.vol_gastado_hci= '';
                                this.cod_balanza= '';
                                this.conc_hci= '';
                                this.dato='';
                                this.cumple='';
                                this.tipoAccion = 1;
                                break;
                            }
                            case 'actualizar':
                            {
                                this.modal=1;
                                this.tituloModal='Actualizar Sulfatos Turbimetría y Espc.';
                                this.tipoAccion=2;
                                this.carbonatos_pcl_id=data['id'];
                                this.idpreparacion=data['idpreparacion'];
                                this.fechaD = data['fecha'];
                                this.codigo_lab=data['codigo_lab'];
                                this.masa_recipiente=data['masa_recipiente'];
                                this.masa_muestra=data['masa_muestra'];
                                this.cod_bureta=data['cod_bureta'];
                                this.observaciones=data['observaciones'];
                                this.vol_gastado_hci= data['vol_gastado_hci'];
                                this.cod_balanza= data['cod_balanza'];
                                this.conc_hci= data['conc_hci'];
                                this.dato= data['dato'];
                                this.cumple= data['cumple'];
                                break;
                            }
                        }
                    }
                }
                this.selectPreparacion();
            }
        },
        mounted() {            
            this.listarCarbonatos_pcl();            
        }
    }
</script>
<style>    
    .modal-content{
        width: 100% !important;
        position: absolute !important;
    }
    .mostrar{
        display: list-item !important;
        opacity: 1 !important;
        position: absolute !important;
        background-color: #3c29297a !important;
    }  
    .datepicker1 {
        background-color:#5d998f; 
        border-radius:15px; 
        overflow:hidden; 
        display:block; 
        margin-bottom:10px; 
        box-shadow:0 0 10px #45b2d3; 
        margin:0 auto; 
        margin-top:10px;
        font-family: 'Lato', sans-serif;
        color:rgb(46, 93, 155);
    }        
</style>
<style lang="scss"> @import './resources/assets/scss/bootstrap.scss';
@import './resources/assets/scss/coreui.scss';
/*@import 'node_modules/bootstrap/scss/bootstrap';
@import 'node_modules/bootstrap-vue/src/index.scss';*/

$body-font-family: 'Open Sans', sans-serif;

$checkbox-bg-color-checked: #00bbd6;
$checkbox-border-color-checked: $checkbox-bg-color-checked;
$checkbox-bg-color-unchecked: #fff;
$checkbox-border-color-unchecked:  #757575;

body {
  font-family: $body-font-family;

  .container  {
   // display: flex;
   // flex-wrap: wrap;
   // justify-content: center;
    .box {
      //text-align: center;
      width: 250px;
      padding: 10px;
      border-radius: 0px;      
    }
  }
 
  .ta {
    padding-left: 25px;
  }

  .checkbox-group {
    input[type=checkbox] {
      display: none;
      @mixin checkbox-structure($width: 25px, $height: 25px, $top: -1px, $left: '', $border-color: '', $background-color: '') {
        content: "";
        position: absolute;
        width: $width;
        height: $height;
        top: $top;
        border-radius: 2px;
        @if $border-color!='' {
          border: 2px solid $border-color;
        }

        @if $background-color!='' {
          background-color: $background-color;
        }

        transition: .2s;
      }

      &:not(:checked) + label::before {
        @include checkbox-structure($border-color: $checkbox-border-color-unchecked, $background-color: $checkbox-bg-color-unchecked);
         left: 0;
      }

      &:checked + label {
        &::before {
          @include checkbox-structure($border-color: $checkbox-border-color-checked, $background-color: $checkbox-bg-color-checked);

          left: 0;
        }
        &::after {
          @include checkbox-structure($width: 15px, $height: 19px);

          left: 5px;
          border-top: 2px solid transparent;
          border-left: 2px solid transparent;
          border-right: 2px solid #fff;
          border-bottom: 2px solid #fff;
          -webkit-transform: rotate(36deg);
          -ms-transform: rotate(36deg);
          transform: rotate(36deg);
        }
      }
    }
    label {
      cursor: pointer;
      position: relative;
    }
  }  
}
</style>