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

const idTacheASupprimer = ref();
const nomTacheASupprimer = ref();

const selectTacheASupprimer = (id, nom) => {
  idTacheASupprimer.value = id;
  nomTacheASupprimer.value = nom;
};

const supprimerTache = (nom) => {
  taches.value = taches.value.filter((tache) => tache.nom !== nom);
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
    <ul class="list-group mt-3" v-if="taches.length > 0">
      <li
        v-for="(tache, index) in taches"
        class="list-group-item d-flex gap-3 align-items-baseline justify-content-between"
      >
        <div class="d-flex gap-2">
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
        </div>
        <button
          @click="selectTacheASupprimer(index, tache.nom)"
          class="btn btn-sm btn-danger"
          data-bs-toggle="modal"
          data-bs-target="#delete-modal"
        >
          Supprimer
        </button>
      </li>
    </ul>
    <p v-else>Il n'y a pas encore de tâche.</p>
  </div>

  <!-- Modal de suppression -->
  <div class="modal" tabindex="-1" id="delete-modal">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Supprimer une tâche</h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <p>
            Êtes-vous certain de vouloir supprimer la tâche "{{
              nomTacheASupprimer
            }}" ?
          </p>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-secondary"
            data-bs-dismiss="modal"
          >
            Annuler
          </button>
          <button
            @click="supprimerTache(nomTacheASupprimer)"
            type="button"
            class="btn btn-danger"
            data-bs-dismiss="modal"
          >
            Supprimer
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
