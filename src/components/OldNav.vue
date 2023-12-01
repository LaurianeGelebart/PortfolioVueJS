<template>
      <nav id="navbar" class="navbar">
        <button type="button" data-target="#collapseNavbar" data-toggle="collapse" class="navbar-toggler">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="navbar-collapse" id="collapseNavbar">
          <ul class="navbar-nav">
            <li class="nav-item">
              <a class="nav-link navbar_element" id="navbar_moi" href="#WhoAmI">Qui suis-je ?</a>
            </li>
            <li class="nav-item">
              <a class="nav-link navbar_element" id="navbar_real" href="#Gallery">Mes Réalisations</a>
            </li>
            <li class="nav-item">
              <a class="nav-link navbar_element" id="navbar_contact" href="#Contacts">Contact</a>
            </li>
          </ul>
        </div>
      </nav>
</template>

<script>
export default {
  name: 'NavbarPage',
  props: {},
  emits: {},
  data() {
    return {}
  },
  methods: {}
}
</script>

<style scoped>

.navbar {
  padding: 1vw 0;
  z-index: 3;
  display: flex;
  /* position: fixed !important; */
  align-items: center;
  background-color: white;
  width: 100%;
  height: 3vw;
  transition: all 0.25s ease-in-out;
}


.navbar-collapse {
  display: flex;
  flex-basis: auto;
  justify-content: flex-end;
  align-items: center;
  flex-grow: 1;
}

.navbar-nav {
  display: flex;
  flex-direction: row;
  padding-left: 0;
  margin-bottom: 0;
  list-style: none;
}

.navbar-toggler {
  padding: .25rem .75rem;
  font-size: 1.25rem;
  line-height: 1;
  background-color: transparent;
  border: 1px solid transparent;
  border-radius: .25rem;
}

.navbar_element {
  font-family: 'Raleway', sans-serif;
  text-transform: uppercase;
  color: black;
  font-size: 2.5vh;
  margin: 0 2vw;
  padding: 0;
  text-decoration: none;
  background: linear-gradient(to top, #FFDC00 0%, #FFDC00 40%, transparent 2.01%) no-repeat left bottom / 40% 100%;
  transition: background-size .4s;
}

.navbar_element:hover {
  color: black !important;
  background-size: 100% 100%;
}

.fleche {
  fill: #FFDC00;
  position: fixed;
  right: 3%;
  bottom: 4%;
  transition: 0.2s cubic-bezier(0.77, 0.2, 0.05, 1.0);
  z-index: 3;
  width: 3vw;
}

.fleche:hover {
  fill: #079386;
}

.navbar.scroll {
  padding-top: 0;
  padding-bottom: 0;
  opacity: 0.9;
}

.navbar.scroll .logo_blanc {
  width: 50px;
}


@media screen and (max-width: 1024px) {}

@media screen and (max-width: 768px) {}
</style>