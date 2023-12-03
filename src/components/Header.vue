<template>
  <header>
    <Header3DInterface v-if="isLaptop && user3D" />
    <Header2DInterface v-if="!user3D" />
  </header>
</template>

<script>
import Header3DInterface from "@/components/3DInterface.vue";
import Header2DInterface from "@/components/2DInterface.vue";

export default {
  name: "HeaderPage",
  components: { Header3DInterface, Header2DInterface },
  props: {
    user3D: { type: Boolean, required: true, default: false },
    isLaptop: { type: Boolean, required: true, default: false },
  },
  emits: {},
  data() {
    return {};
  },
  methods: {},
  beforeMount() {
    // console.log(this.user3D)
  },
};
</script>


<style scoped>
/* header {
  color: var(--background-color-primary);
  position: relative;
  display: flex !important;
  flex-direction: row;
  justify-content: center;
  padding-top: 4rem;
} */



@media screen and (max-width: 1024px) {
}

@media screen and (max-width: 768px) {
}
</style>
  