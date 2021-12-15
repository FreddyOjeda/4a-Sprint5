<template>
    
    <div v-if="loaded" class="information">
        <div class="contenedor">
            <h1>Productos A Tu Alcance</h1>
            <div class="scroll">
                <tr v-for="ve in listaProductos" v-bind:key="ve.id">
                    <h2>Nombre: <span>{{ve.nombre}}</span></h2>
                    <h2>Descripcion: <span>{{ve.descripcion}}</span></h2>
                    <h2>Precio: $<span>{{ve.precio}}</span></h2>
                    <h2>Cant. Disponibles: <span>{{ve.stock}}</span></h2>
                    <button class="apartar">Agregar Al Carrito</button>
                    <h2>____________________________________</h2>           
                </tr>
            </div>
        </div>
        <img class="bart" src="https://forum.ikariam.gameforge.com/attachment/293-ae3-png/" width="220" height="440">
    </div>
</template>


<script>
import jwt_decode from "jwt-decode"
import axios from 'axios'

export default {
    name: "Productos",

    data: function(){
        return {
            index:-1,
            product:{
                nombre: "",
                descripcion: "",
                precio: "",
                stock:"",
            },
            loaded: false,
            listaProductos:[]
        }
    },

    methods: {
        getData: async function () {

            if (localStorage.getItem("token_access") === null || localStorage.getItem("token_refresh") === null) {
            this.$emit('logOut');
            return;
        }

        await this.verifyToken();
        let token = localStorage.getItem("token_access");
        let productId = jwt_decode(token).user_id.toString();

        axios.get(`http://127.0.0.1:8000/detail_all_products/${productId}/`, {headers: {'Authorization': `Bearer ${token}`}})

            .then((result) => {
                
                this.listaProductos= result.data;
                //this.product.name = listaProductos[this.index+1].name;
                //this.product.email = listaProductos[this.index+1].email;
                //this.product.productname = listaProductos[this.index+1].productname;
                //this.product.celular = listaProductos[this.index+1].celular;
                //this.product.fechaNacimiento = listaProductos[this.index+1].fechaNacimiento;
                this.loaded = true;
                })
            .catch(() => {
                this.$emit('logOut');
            });
    },

    verifyToken: function () {
        return axios.post("http://127.0.0.1:8000/refresh/", {refresh: localStorage.getItem("token_refresh")}, {headers: {}})

            .then((result) => {
                localStorage.setItem("token_access", result.data.access);
            })
            .catch(() => {
                this.$emit('logOut');
            });
    },

    mostrar: function(i){
        this.index=i;
        this.nombre = listaProduct[i].nombre;
        this.descripcion = listaProduct[i].descripcion;
        this.precio = listaProduct[i].precio;
        this.stock = listaProduct[i].stock;
    }
},
    created: async function(){
        this.getData();
    },
}
</script>

<style>
    .bart{
        position: absolute;
        left:-5px; /*A la derecha deje un espacio de 0px*/
        bottom:80px; /*Abajo deje un espacio de 0px*/
    }
    .information {
        margin: 0;
        padding: 0%;
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .information h1{
        font-size: 40px;
        color: #0f1316;
    }

    .information h2{
        font-size: 25px;
        color: #283747;
    }

    .information span{
        color: crimson;
        font-weight: bold;
    }

    .scroll{
        overflow-y: auto;
        overflow-x:hidden;
        max-width: 55vw;
        max-height: 40vh;
        background: cornsilk;
        left:438px; /*A la derecha deje un espacio de 0px*/
        bottom:80px; /*Abajo deje un espacio de 0px*/
    }

    .contenedor{
        border: 3px solid #283747;
        border-radius: 10px;
        backdrop-filter: blur(12px);
        width: 40%;
        height: 99%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }
</style>