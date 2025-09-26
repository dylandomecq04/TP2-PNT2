<script setup>
import { ref, computed } from 'vue'

// Datos de ejemplo (podés dejar los tuyos)
const personas = ref([
  { nombre: "Daniel", apellido: "Sanchez", correo: "danielsanchez68@hotmail.com", dni: "20442873" },
  { nombre: "Juan",   apellido: "Perez",   correo: "j@p.gmail.com",               dni: "12345678" },
  { nombre: "Ana",    apellido: "Suarez",  correo: "a@s.gmail.com",                dni: "87654321" },
  { nombre: "Dylan",    apellido: "Domecq",     correo: "d@gmail.com",     dni: "42587167" },
  { nombre: "Ana",    apellido: "Domecq",     correo: "d@gmail.com",     dni: "42587167" }
])

// Filtros controlados
const filtroNombre = ref("")
const filtroDni    = ref("")

// Longitud mínima para que un filtro "cuente"
const MIN = 3

// Estados de advertencia (tienen texto pero < 3)
const warningNombre = computed(() => filtroNombre.value !== "" && filtroNombre.value.length < MIN)
const warningDni    = computed(() => filtroDni.value !== "" && filtroDni.value.length < MIN)

// Aplicación de filtros:
// - Si un filtro está vacío o tiene menos de 3 chars, NO se aplica (queda "deshabilitado" lógicamente).
// - Si ambos están activos (>=3), se exige que se cumplan LOS DOS (AND).
const personasFiltradas = computed(() => {
  const nombreActivo = filtroNombre.value.trim().length >= MIN
  const dniActivo    = filtroDni.value.trim().length    >= MIN

  const nom = filtroNombre.value.trim().toLowerCase()

  return personas.value.filter(p => {
    const nombreCompleto = `${p.nombre} ${p.apellido}`.toLowerCase()
    const matchNombre = !nombreActivo || nombreCompleto.includes(nom)
    const matchDni    = !dniActivo    || String(p.dni).includes(filtroDni.value.trim())
    return matchNombre && matchDni
  })
})

// Helper para mostrar nombre completo
const getNombreCompleto = (p) => `${p.nombre} ${p.apellido}`
</script>

<template>
  <div class="container py-4">
    <h1 class="mb-3">Buscador de Personas</h1>

    <!-- Filtros -->
    <div class="row g-3">
      <div class="col-md-6">
        <label class="form-label">Nombre o Nombre + Apellido</label>
        <input
          type="text"
          class="form-control"
          placeholder="Ej: Ana Suarez"
          v-model="filtroNombre"
        />
      </div>
      <div class="col-md-6">
        <label class="form-label">DNI</label>
        <input
          type="text"
          class="form-control"
          placeholder="Ej: 20442873"
          v-model="filtroDni"
        />
      </div>
    </div>

    <!-- Alert: cuando alguno tiene texto pero < 3 -->
    <div v-if="warningNombre || warningDni" class="alert alert-warning mt-3" role="alert">
      Ingrese al menos 3 caracteres en los filtros.
    </div>

    <hr class="my-4" />

    <!-- Resultados -->
    <h5 class="mb-3">Resultados ({{ personasFiltradas.length }})</h5>

    <div class="row g-3 mt-2">
  <div
    class="col-12 col-sm-6 col-md-4 col-lg-3"
    v-for="(persona, i) in personasFiltradas"
    :key="i"
  >
    <div class="card h-100">
      <div class="card-body">
        <h5 class="card-title">{{ getNombreCompleto(persona) }}</h5>
        <p class="card-text mb-1"><strong>DNI:</strong> {{ persona.dni }}</p>
        <a class="card-link" :href="`mailto:${persona.correo}`">{{ persona.correo }}</a>
      </div>
    </div>
  </div>
</div>

  </div>
</template>

<style scoped>

</style>


