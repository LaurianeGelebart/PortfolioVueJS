<template>
  <div class="scene" ref="scene">
    <div v-for="model in models" :key="model.position[0]">
      <div v-if="model.info" class="textPanel" id="textPanel">
        <p v-if="model.info.isDisplay">Contenu du panneau de texte.</p>
      </div>
    </div>
  </div>
</template>
  
  <script>
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

export default {
  data() {
    return {
      scene: null,
      camera: null,
      renderer: null,
      raycaster: null,
      time: 0,
      models: [
        {
          model: null,
          position: [0, 0, 0],
          info: {
            text: "Contenu du panneau de texte.",
            isDisplay: false,
          },
        },
        {
          model: null,
          position: [0.2, 0.3, 0.3],
          info: null,
        },
        {
          model: null,
          position: [0.4, -0.5, 0],
          info: {
            text: "Contenu du panneau de texte le deuxième.",
            isDisplay: false,
          },
        },
      ],
    };
  },
  mounted() {
    this.initThree();
    this.createRenderer();
    this.createScene();
    this.createCamera();
    this.addObjects();
    this.animate();
  },
  methods: {
    initThree() {
      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
    },
    //--------------------------------------------------createScene--------------------------------------------------//
    createScene() {
      // Créer une lumière ponctuelle avec une couleur et une intensité
      const pointLight = new THREE.PointLight(0xF7E3C1, 50);
      pointLight.position.set(2, 2, 0);
      this.scene.add(pointLight);
      const ambientLight = new THREE.AmbientLight(0xffffff, 2);
      this.scene.add(ambientLight);
      this.scene.background = new THREE.Color(0xffffff);
    },
    //--------------------------------------------------createCamera--------------------------------------------------//
    createCamera() {
      this.camera.position.set(0, 0, 1);
      this.camera.lookAt(0, 0, 0);
    },
    //--------------------------------------------------createRenderer--------------------------------------------------//
    createRenderer() {
      this.renderer = new THREE.WebGLRenderer();
      this.mouse = new THREE.Vector2();
      this.raycaster = new THREE.Raycaster();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.$refs.scene.appendChild(this.renderer.domElement);
      this.$refs.scene.addEventListener("mousemove", this.onMouseMove, false);
      this.$refs.scene.addEventListener("click", this.onCanvasClick, false);
    },
    //--------------------------------------------------AddObject--------------------------------------------------//
    addObjects() {
      const loader = new GLTFLoader();
      loader.load(
        "./portfolio3D.gltf",
        (gltf) => {
          const model1 = gltf.scene;
          if (model1) {
            model1.position.set(0, 0, 0);
            model1.scale.set(1, 1, 1);
            model1.rotation.y = -Math.PI / 2;
            model1.myId = "model1";
            this.models[0].model = model1;
            this.scene.add(model1);
            // console.log(
            // "Modèle 1 chargé avec succès : " + this.models[0].model.uuid
            // );
            // console.log("Modèle 1 chargé avec succès : " + model1.uuid);

            // this.animateModelSize();
          } else {
            console.error("Erreur de chargement du modèle");
          }
        },
        undefined,
        (error) => {
          console.error("Erreur de chargement du modèle : " + error);
        }
      );
     
      this.loaderGLTF("./cadres.gltf", 1)
      this.loaderGLTF("./livres.gltf", 2)
      this.loaderGLTF("./skate.gltf", 3)
      // this.loaderGLTF("./pomme.gltf", 4)
      // this.loaderGLTF("./potCrayons.gltf", 5)
      // this.loaderGLTF("./coussins.gltf", 6)
      // this.loaderGLTF("./plante.gltf", 7)
      // this.loaderGLTF("./laptop.gltf", 8)
    },
    //--------------------------------------------------loaderGLTF--------------------------------------------------//
    loaderGLTF(path, id){
      const loader = new GLTFLoader();
      loader.load(
        path,
        (gltf) => {
          const model = gltf.scene;
          if (model) {
            model.position.set(0, 0, 0);
            model.scale.set(1, 1, 1);
            model.rotation.y = -Math.PI / 2;
            model.myId = "model1";
            this.models[id].model = model;
            this.scene.add(model);
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
      const canvasBounds = this.$refs.scene.getBoundingClientRect();
      this.mouse.x =
        ((event.clientX - canvasBounds.left) / canvasBounds.width) * 2 - 1;
      this.mouse.y =
        (-(event.clientY - canvasBounds.top) / canvasBounds.height) * 2 + 1;
      this.camera.lookAt(this.mouse.x * 0.1, this.mouse.y * 0.1, 0);
    },
    //--------------------------------------------------onCanvasClick--------------------------------------------------//
    onCanvasClick(event) {
      const canvasBounds = this.$refs.scene.getBoundingClientRect();
      const x =
        ((event.clientX - canvasBounds.left) / canvasBounds.width) * 2 - 1;
      const y =
        -((event.clientY - canvasBounds.top) / canvasBounds.height) * 2 + 1;
      // Créez un vecteur pour stocker les coordonnées du clic en 3D
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
      // Mise à jour du rayon
      this.raycaster.set(rayOrigin, rayDirection);
      // Lancez le rayon pour détecter les intersections
      const intersects = this.raycaster.intersectObjects(
        this.scene.children,
        true
      );
      // Vérifiez si des objets ont été cliqués
      if (intersects.length > 0) {
        const clickedObject = intersects[0].object;
        console.log(clickedObject.myId);
        console.log(this.models[0].model.myId);
        if (clickedObject.myId == this.models[0].model.myId)
          console.log("C'est l'objet 0");
        this.models[0].info.isDisplay = !this.models[0].info.isDisplay;
      }
    },
    //--------------------------------------------------animate--------------------------------------------------//
    animate() {
      requestAnimationFrame(this.animate);
      this.renderer.render(this.scene, this.camera);
    },
    //--------------------------------------------------animateModelSize--------------------------------------------------//
    animateModelSize() {
      // Cette fonction sera appelée à chaque trame d'animation
      requestAnimationFrame(this.animateModelSize);
      // Mise à jour de la taille du modèle en fonction du temps
      this.time += 0.01;
      const scaleFactor = (Math.sin(this.time * 5) + 1) * 0.01;
      for (let i = 0; i < this.models.length; i++) {
        if (this.models[i].info && this.models[i].model) {
          this.models[i].model.scale.set(
            0.1 + scaleFactor,
            0.1 + scaleFactor,
            0.1 + scaleFactor
          );
        }
      }
    },
  },
};
</script>

<style scoped>
.scene {
  position: relative;
}
.textPanel {
  background: rgba(0, 0, 0, 0.5);
  color: white;
  position: absolute;
  top: 25%;
}
</style>
  