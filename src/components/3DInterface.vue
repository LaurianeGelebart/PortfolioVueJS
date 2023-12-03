<template>
  <div class="canvas3D">
    <Loader v-if="isLoading" />
    <div v-show="!isLoading" class="scene" ref="scene"></div>
  </div>
</template>
  
  <script>
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { getModels } from "@/api/getData.js";
import Loader from "@/components/Loader.vue";

export default {
  name: "Header3DInterface",
  components: { Loader },
  data() {
    return {
      isLoading: true,
      models: null,
      scene: null,
      camera: null,
      renderer: null,
      raycaster: null,
      isDark: false,
      mouseMouveLock: true,
      mouseVector: new THREE.Vector2(),
      raycasterMove: new THREE.Raycaster(),
      time: 0,
      pointLight: null,
      width: window.innerWidth,
      height: window.innerHeight,
    };
  },
  mounted() {
    this.retrieveData();
    this.initThree();
    this.createRenderer();
    this.createScene();
    this.createCamera();
    this.animate();
    setTimeout(() => {
      this.isLoading = false;
    }, 2000);
  },
  // watch() {},
  methods: {
    async retrieveData() {
      this.models = await getModels();
      for (let i = 0; i < this.models.objects.length; i++) {
        this.loaderGLTF(
          this.models.objects[i].path,
          this.models.objects[i].position,
          false
        );
      }
      for (let i = 0; i < this.models.letters.length; i++) {
        this.loaderGLTF(
          this.models.letters[i].path,
          this.models.letters[i].position,
          true
        );
      }
    },
    initThree() {
      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera(
        55,
        this.width / this.height,
        0.1,
        100
      );
    },
    //--------------------------------------------------createScene--------------------------------------------------//
    createScene() {
      this.pointLight = new THREE.PointLight(0xf7e3c1, 50);
      this.pointLight.position.set(2, 2, 0);
      this.scene.add(this.pointLight);
      const ambientLight = new THREE.AmbientLight(0xffffff, 2);
      this.scene.add(ambientLight);
      this.scene.background = new THREE.Color(0xfafafa);
    },
    //--------------------------------------------------createCamera--------------------------------------------------//
    createCamera() {
      this.camera.position.set(0, 0, 1);
      this.camera.lookAt(0.1, -0.1, 0);
    },
    //--------------------------------------------------createRenderer--------------------------------------------------//
    createRenderer() {
      this.renderer = new THREE.WebGLRenderer();
      this.mouse = new THREE.Vector2();
      this.raycaster = new THREE.Raycaster();
      this.renderer.setSize(this.width, this.height);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.$refs.scene.appendChild(this.renderer.domElement);
      this.$refs.scene.addEventListener("mousemove", this.onMouseMove, false);
      this.$refs.scene.addEventListener("click", this.onCanvasClick, false);
    },
    //--------------------------------------------------loaderGLTF--------------------------------------------------//
    loaderGLTF(path, position, isLetter) {
      const loader = new GLTFLoader();
      loader.load(
        path,
        (gltf) => {
          const model = gltf.scene;
          if (model) {
            var box = new THREE.Box3().setFromObject(model);
            box.getCenter(model.position); // this re-sets the mesh position
            model.position.multiplyScalar(-1);
            model.isLetter = isLetter;
            model.time = 0;
            if (!isLetter && model.name != "etu") {
              model.rotation.y -= 0.15;
            }
            const pivot = new THREE.Object3D();
            pivot.position.set(position[0], position[1], position[2]);
            pivot.rotation.y = -Math.PI / 2;
            pivot.scale.set(1, 1, 1);
            pivot.add(model);
            this.scene.add(pivot);
          } else {
            console.error("Erreur de chargement du modèle");
          }
        },
        undefined,
        (error) => {
          console.error("Erreur de chargement du modèle : " + error);
        }
      );
    },
    //--------------------------------------------------onMouseMove--------------------------------------------------//
    onMouseMove(event) {
      // if (!this.mouseMouveLock) {
      // const canvasBounds = this.$refs.scene.getBoundingClientRect();
      // this.mouse.x =
      //   ((event.clientX - canvasBounds.left) / canvasBounds.width) * 2 - 1;
      // this.mouse.y =
      //   (-(event.clientY - canvasBounds.top) / canvasBounds.height) * 2 + 1;
      // this.camera.lookAt(
      //   this.mouse.x * 0.1 + 0.1,
      //   this.mouse.y * 0.1 - 0.1,
      //   0
      // );
      const mouseX = (event.clientX / window.innerWidth) * 2 - 1;
      const mouseY = -(event.clientY / window.innerHeight) * 2 + 1;
      this.mouseVector.set(mouseX, mouseY);
      this.raycasterMove.setFromCamera(this.mouseVector, this.camera);
      const intersects = this.raycasterMove.intersectObjects(
        this.scene.children
      );
      if (intersects.length > 0) {
        const hoveredObject = intersects[0].object.parent;
        if (hoveredObject.isLetter) {
          if (hoveredObject.time === 0) {
            hoveredObject.time = this.time + Math.random() * 0.1 + 0.3;
            // console.log(hoveredObject.time);
          }
        }
      }
      // }
    },
    turnLetter(hoveredObject) {
      hoveredObject.parent.rotation.y += 0.1;
    },
    //--------------------------------------------------onCanvasClick--------------------------------------------------//
    onCanvasClick(event) {
      const canvasBounds = this.$refs.scene.getBoundingClientRect();
      const x =
        ((event.clientX - canvasBounds.left) / canvasBounds.width) * 2 - 1;
      const y =
        -((event.clientY - canvasBounds.top) / canvasBounds.height) * 2 + 1;
      const mouse3D = new THREE.Vector3(x, y, 0.5);
      // Définissez l'origine du rayon à la position de la caméra
      const rayOrigin = new THREE.Vector3();
      this.camera.getWorldPosition(rayOrigin);
      // Définissez la direction du rayon
      const rayDirection = mouse3D
        .clone()
        .unproject(this.camera)
        .sub(rayOrigin)
        .normalize();
      this.raycaster.set(rayOrigin, rayDirection);
      const intersects = this.raycaster.intersectObjects(
        this.scene.children,
        true
      );
      // Vérifiez si des objets ont été cliqués
      if (intersects.length > 0) {
        const clickedObject = intersects[0].object;
        // Si c'est lampe
        if (clickedObject.name == "Cylinder008_1") {
          let theme = "light-theme";
          if (this.isDark) {
            this.pointLight.intensity += 100;
            this.scene.background = new THREE.Color(0xfafafa);
          } else {
            this.pointLight.intensity -= 100;
            this.scene.background = new THREE.Color(0x222222);
            theme = "dark-theme";
          }
          this.isDark = !this.isDark;
          localStorage.setItem("user-theme", theme);
          document.documentElement.className = theme;
        }
      }
      // else {
      //   this.mouseMouveLock = !this.mouseMouveLock;
      // }
    },
    //--------------------------------------------------animate--------------------------------------------------//
    animate() {
      // Cette fonction sera appelée à chaque trame d'animation
      requestAnimationFrame(this.animate);
      // Mise à jour de la taille et de la position des modèles en fonction du temps
      this.time += 0.01;
      // const scaleFactor = (Math.sin(this.time * 5) + 1) * 0.005;
      for (let i = 0; i < this.scene.children.length; i++) {
        const pivot = this.scene.children[i];
        if (pivot && pivot.children[0]) {
          const model = pivot.children[0]; // Le modèle est le premier enfant du pivot
          if (model.isLetter) {
            if (model.time != 0) {
              pivot.rotation.y += Math.PI / 10;
              if (model.time < this.time) model.time = 0;
            }
          } else {
            // model.scale.set(1 + scaleFactor, 1 + scaleFactor, 1 + scaleFactor);
          }
        }
      }
      this.renderer.render(this.scene, this.camera);
    },
  },
};
</script>

<style scoped>
.cursor-pointer {
  cursor: pointer;
}
.canvas3D{
  height: 100vh;
}
</style>
  