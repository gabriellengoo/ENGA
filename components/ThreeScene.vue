<template>
  <div ref="sceneContainer" class="webgl-container"></div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';

const sceneContainer = ref(null);

onMounted(() => {
  if (!sceneContainer.value) {
    console.error("Scene container ref is not available.");
    return;
  }

  // Scene, Camera, Renderer
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  sceneContainer.value.appendChild(renderer.domElement);

  // Add Lights
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);

  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(5, 5, 5);
  scene.add(directionalLight);

  // Add Debug Cube (Remove this once the model loads)
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
  const cube = new THREE.Mesh(geometry, material);
  scene.add(cube);

  // Load the 3D Model
  const loader = new GLTFLoader();
  loader.load(
    '/models/model.glb',
    (gltf) => {
      const model = gltf.scene;
      model.scale.set(1, 1, 1);
      scene.add(model);
      console.log("Model loaded successfully.");
    },
    undefined,
    (error) => {
      console.error('Error loading the model:', error);
    }
  );

  // Camera Position
  camera.position.z = 5;

  // Render Loop
  function animate() {
    requestAnimationFrame(animate);
    // cube.rotation.y += 0.01; // Rotate the cube for debugging
    renderer.render(scene, camera);
  }
  animate();

  // Resize Handling
  window.addEventListener('resize', () => {
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
  });
});
</script>

<style>
.webgl-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
</style>
