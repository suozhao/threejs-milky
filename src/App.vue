<script setup>
import * as THREE from 'three';


const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 200;

const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const sunGeometry = new THREE.SphereGeometry(10, 32, 32);
const sunTexture = new THREE.TextureLoader().load('1.jpg');
const sunMaterial = new THREE.MeshPhongMaterial({ map: sunTexture });
const sun = new THREE.Mesh(sunGeometry, sunMaterial);
scene.add(sun);

const planetsData = [
  {
    name: 'Mercury',
    radius: 5, // 半径（单位：单位长度）
    texture: '2.jpg', // 行星纹理贴图
    distance: 30, // 公转距离（单位：单位长度）
    rotationSpeed: 0.01, // 自转速度（单位：弧度/帧）
    revolutionSpeed: 0.02 // 公转速度（单位：弧度/帧）
  },
  {
    name: 'Venus',
    radius: 7,
    texture: '3.jpg',
    distance: 40,
    rotationSpeed: 0.02,
    revolutionSpeed: 0.015
  },
  {
    name: 'Earth',
    radius: 7.5,
    texture: '4.jpg',
    distance: 50,
    rotationSpeed: 0.03,
    revolutionSpeed: 0.01
  },
  {
    name: 'Mars',
    radius: 6,
    texture: '5.jpg',
    distance: 60,
    rotationSpeed: 0.04,
    revolutionSpeed: 0.008
  },
  {
    name: 'Jupiter',
    radius: 15,
    texture: '6.jpg',
    distance: 70,
    rotationSpeed: 0.02,
    revolutionSpeed: 0.005
  },
  {
    name: 'Saturn',
    radius: 12,
    texture: '7.jpg',
    distance: 80,
    rotationSpeed: 0.01,
    revolutionSpeed: 0.004
  },
  {
    name: 'Uranus',
    radius: 10,
    texture: '8.jpg',
    distance: 90,
    rotationSpeed: 0.015,
    revolutionSpeed: 0.003
  },
  {
    name: 'Neptune',
    radius: 9,
    texture: '9.jpg',
    distance: 100,
    rotationSpeed: 0.012,
    revolutionSpeed: 0.002
  }
];

const planetMaterials = planetsData.map(planet => {
  const texture = new THREE.TextureLoader().load(planet.texture);
  return new THREE.MeshPhongMaterial({ map: texture });
});

const planets = planetMaterials.map((material, index) => {
  const planetGeometry = new THREE.SphereGeometry(planetsData[index].radius, 32, 32);
  const planet = new THREE.Mesh(planetGeometry, material);
  planet.distance = planetsData[index].distance;
  planet.rotationSpeed = planetsData[index].rotationSpeed;
  planet.revolutionSpeed = planetsData[index].revolutionSpeed;
  scene.add(planet);
  return planet;
});

const ambientLight = new THREE.AmbientLight(0x404040);
scene.add(ambientLight);

const pointLight = new THREE.PointLight(0xffffff, 1);
pointLight.position.set(10, 10, 10);
scene.add(pointLight);

function animate() {
  requestAnimationFrame(animate);

  planets.forEach((planet, index) => {
    const revolutionSpeed = planet.revolutionSpeed;
    const rotationSpeed = planet.rotationSpeed;
    const distance = planet.distance;

    // 行星公转
    planet.position.x = Math.cos(revolutionSpeed) * distance;
    planet.position.y = Math.sin(revolutionSpeed) * distance;

    // 行星自转
    planet.rotation.y += rotationSpeed;
  });

  renderer.render(scene, camera);
}

animate();
</script>

<template>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
}
#scene,body,html {
  width: 100%;
  height: 100%;
}
</style>
