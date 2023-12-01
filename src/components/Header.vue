<template>
  <header>
    <div class="lesTitres">
      <h1 class="titre">Lauriane Gélébart</h1>
      <h2 class="sous_titre">- Étudiante web et multimédia -</h2>
    </div>
    <div class="dessin">
      <img src="@/img/MOI.png" class="illu">
    </div>
  </header>
</template>

<script>
export default {
  name: 'HeaderPage',
  props: {},
  emits: {},
  data() {
    return {};
  },
  methods: {},
  beforeMount() { }
}
</script>


<style scoped>
header {
  color: var(--background-color-primary);
  position: relative;
  display: flex !important;
  flex-direction: row;
  justify-content: center;
  padding-top: 4rem;
}

.lesTitres {
  text-align: center;
  margin: -3vw -5vw 0 0;
  width: 40%;
}

.dessin {
  width: 65%;
  margin: 0 0 0 -5vw;
}

.illu {
  width: 100%;
  height: auto;
}

.titre {
  color: var(--accent-color-hover);
  transition: 0.8s cubic-bezier(0.77, 0.2, 0.05, 1.0);
  font-size: 6.8vw;
  font-family: 'PRIMETIME', sans-serif;
  animation: arrive 2s ease-in-out;
  padding: 10vh 2vw 3vh;
  margin: 0;
}

.margeTit {
  margin-left: 0;
  margin-right: 0;
}

.sous_titre {
  color: var(--text-primary-color);
  padding: 0;
  transition: 0.8s cubic-bezier(0.77, 0.2, 0.05, 1.0);
  font-size: 1.4vw;
  font-family: 'Raleway', sans-serif;
  text-transform: uppercase;
  font-weight: 100;
  animation: arrive2 2s ease-in-out;
  opacity: 1;
}

@keyframes arrive {
  0% {
    opacity: 0;
  }

  30% {
    opacity: 1;
  }
}

@keyframes arrive2 {
  0% {
    transform: translateY(90%);
    opacity: 0;
  }

  10% {
    transform: translateY(90%);
    opacity: 0;
  }

  60% {
    opacity: 1;
  }

  100% {
    transform: translateY(0);
    opacity: 1;
  }
}

@media screen and (max-width: 1024px) {
  header {
    flex-direction: column;
  }

  .illu {
    margin: 20% 0;
  }

  .lesTitres {
    /* margin: 10vh 0 0; */
    width: 100vw;
  }

  .dessin {
    width: 90vw;
    margin: 0;
    padding: 0 3vw;
  }

  .titre {
    font-size: 7rem;
  }

  .sous_titre {
    font-size: 1.5rem
  }
}

@media screen and (max-width: 768px) {
  .titre {
    font-size: 3.5rem;
  }

  .sous_titre {
    font-size: 1rem
  }
}
</style>
  