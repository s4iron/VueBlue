<template>
    <div>
        

        <form @submit.prevent='editarNota(nota)' v-if="editarActivo">
            <h3>Editar</h3>
            <input type='text' placeholder='Nombre' class='form-control mb-2' v-model='nota.nombre'>
            <input type='text' placeholder='Descripcion' class='form-control mb-2' v-model='nota.descripcion'>
            <button class='btn btn-success' type='submit'>Actualizar</button>
            <button class='btn btn-danger' @click="cancelar">Cancelar</button>
        </form>

        <form @submit.prevent='agregar' v-else>
            <h3>Agregar tareas</h3>
            <input type='text' placeholder='Nombre' class='form-control mb-2' v-model='nota.nombre'>
            <input type='text' placeholder='Descripcion' class='form-control mb-2' v-model='nota.descripcion'>
            <button class='btn btn-primary' type='submit'>Agregar</button>
        </form>

        <hr class='mt-3'>
        <h3>Listado notas</h3>
        <ul class='list-group my-2'>
            <li class='list-group-item'
                v-for='(item,index) in notas' :key='index'>
                <span class='badge badge-primary float-right'>{{item.updated_at}}   </span>
                <p>{{item.nombre}} => {{item.descripcion}}</p>
                <button class="btn btn-danger btn-sm" @click='eliminarNota(item,index)'>Borrrar</button>
                <button @click='editarFormulario(item)' 
                class="btn btn-warning btn-sm">
                Editar</button>
            </li>
        </ul>

    </div>
</template>
<script>
    export default{
        data(){
            return{
                notas:[],
                nota:{nombre:'',descripcion:''},
                editarActivo:false
            }//return
        },//data
        created(){
            axios.get('/notas')
            .then(res=>{
                this.notas=res.data
            })
        },
        methods:{
            agregar(){
                console.log(this.nota.nombre,this.nota.descripcion)
                  if(this.nota.nombre.trim() === '' || this.nota.descripcion.trim() === ''){
                    alert('Debes completar todos los campos antes de guardar');
                    return;
                }
                const params = {
                    nombre:this.nota.nombre,
                    descripcion:this.nota.descripcion
                    }
                    this.nota.nombre=''
                    this.nota.descripcion=''
                axios.post('/notas',params)
                .then(res=>{
                    this.notas.push(res.data)
                })
            },//agregar
            eliminarNota(item,index){
                console.log(index)
                axios.delete(`/notas/${item.id}`)
                    .then(()=>{
                        this.notas.splice(index,1)
                    })
            },
            editarFormulario(item){
                this.editarActivo=true
                this.nota.nombre = item.nombre
                this.nota.descripcion = item.descripcion
                this.nota.id=item.id
            },
            editarNota(item){
                const params={nombre:item.nombre,
                              descripcion:item.descripcion}
                axios.put(`/notas/${item.id}`,params)
                    .then(res=>{
                        const index = this.notas.findIndex(notaBuscar => notaBuscar.id === res.data.id)
                        this.notas[index].nombre=res.data.nombre
                        this.notas[index].descripcion=res.data.descripcion
                    })
                this.nota={nombre:'',descripcion:''}
                this.editarActivo=false
            },
            cancelar(){
                this.nota={nombre:'',descripcion:''}
                this.editarActivo=false
            }
        }
    }
</script>
