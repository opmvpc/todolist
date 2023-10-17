<script setup>
import { ref } from "vue";

// on récupère la string JSON depuis le storage
let tachesEnregistrees = localStorage.getItem("taches");
// on transforme la string en JavaScript valide
// si le storage est vide, on utilise un tableau vide à la place
tachesEnregistrees = JSON.parse(tachesEnregistrees) ?? [];
const taches = ref(tachesEnregistrees);
const nomTache = ref("");
const estInvalide = ref(false);

const ajouter = () => {
  if (nomTache.value.length === 0) {
    estInvalide.value = true;
    return;
  }

  const tache = {
    nom: nomTache.value,
    estFait: false,
  };

  taches.value.push(tache);

  localStorage.setItem("taches", JSON.stringify(taches.value));

  console.log(taches.value);

  nomTache.value = "";
};

const cocher = () => {
  localStorage.setItem("taches", JSON.stringify(taches.value));
};
</script>

<template>
  <nav class="navbar bg-body-tertiary">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">Todo</a>
    </div>
  </nav>
  <div class="container mt-3">
    <h2>Ajouter des tâches</h2>
    <form class="my-3">
      <div class="mb-3">
        <label for="tache" class="form-label">Tâche</label>
        <input
          v-model="nomTache"
          type="text"
          class="form-control"
          :class="{ 'is-invalid': estInvalide }"
          id="tache"
          placeholder="Un truc à faire"
        />
        <div class="text-danger" v-show="estInvalide">
          Le nom de la tâche ne peut pas être vide
        </div>
      </div>
      <div>
        <button @click.prevent="ajouter" class="btn btn-primary">
          Ajouter
        </button>
      </div>
    </form>
    <h2>Liste des tâches</h2>
    <ul class="list-group mt-3">
      <li v-for="(tache, index) in taches" class="list-group-item d-flex gap-3">
        <input
          v-model="tache.estFait"
          class="form-check-input"
          @change="cocher"
          type="checkbox"
          :id="'check-' + index"
        />
        <label class="form-check-label" :for="`check-${index}`">
          {{ tache.nom }}
        </label>
      </li>
    </ul>
  </div>
</template>
