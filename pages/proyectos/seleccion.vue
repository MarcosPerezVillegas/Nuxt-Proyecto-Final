<template>
    <v-container>
        <v-form @submit.prevent="guardar">
            <v-card>
                <v-card-title>
                    {{proyecto.nombre}}
                </v-card-title>
                <v-card-text>
                    Objetivos: {{proyecto.objetivos}}
                    <v-spacer />
                    Carrera: {{carrera}}
                    <v-spacer />
                    Encargado: {{maestro}}
                    <v-spacer />
                    Fecha de inicio: {{proyecto.fechainicio}}
                    <v-spacer />
                    Fecha de termino: {{proyecto.fechafinal}}
                    <v-spacer />
                    Cupos: {{proyecto.alumnos}}
                </v-card-text>
                <v-card-actions>
                    <v-spacer />
                    <v-btn @click="cancelar()" color="red">
                        Cancelar
                    </v-btn>
                    <ConfirmDialog v-if="roles.rol == 'alumno' && proyecto.alumnos != 0" :description="`¿Está seguro de querer unirce al proyecto '${proyecto.nombre}'? Esta acción no se puede deshacer.`" />
                </v-card-actions>
            </v-card>
        </v-form>
    </v-container>
</template>

<script lang="ts">

export default {
    name: 'ProyectosSelect',
    middleware: 'auth',
    data: () => ({
        roles: {},
        proyecto: {},
        maestro: "",
        carrera: ""
    }),

    async beforeMount() {
        this.$store.commit('setTitle', 'Proyectos')
        const id = localStorage.getItem('proId')
        
        try {
            const response = await this.$axios.get(`/proyectos/${id}`)
            this.proyecto = response.data.data
            this.carrera = this.proyecto.Carrera.nombre
            this.maestro = this.proyecto.encargado.nombre
            const responseR = await this.$axios.get('/login')
            this.roles = responseR.data
        } catch (error) {
            this.$nuxt.$emit('show-snackbar', 'red', error.message)
        }
    },

    methods: {
        cancelar() {
            this.$router.push('/proyectos')
        }
    }
}

</script>