<script setup>
import * as THREE from 'three';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import { onMounted } from 'vue';


const scene = new THREE.Scene();
const starrySkyTexture = new THREE.TextureLoader().load('stars.jpeg');
scene.background = starrySkyTexture;
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.y = 200;
camera.position.z = 500;

const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
// 添加控制器
const controls = new OrbitControls(camera, renderer.domElement);

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
    distance: 60,
    rotationSpeed: 0.02,
    revolutionSpeed: 0.015
  },
  {
    name: 'Earth',
    radius: 7.5,
    texture: '4.jpg',
    distance: 90,
    rotationSpeed: 0.03,
    revolutionSpeed: 0.01
  },
  {
    name: 'Mars',
    radius: 6,
    texture: '5.jpg',
    distance: 120,
    rotationSpeed: 0.04,
    revolutionSpeed: 0.008
  },
  {
    name: 'Jupiter',
    radius: 15,
    texture: '6.jpg',
    distance: 150,
    rotationSpeed: 0.02,
    revolutionSpeed: 0.005
  },
  {
    name: 'Saturn',
    radius: 12,
    texture: '7.jpg',
    distance: 180,
    rotationSpeed: 0.01,
    revolutionSpeed: 0.004
  },
  {
    name: 'Uranus',
    radius: 10,
    texture: '8.jpg',
    distance: 210,
    rotationSpeed: 0.015,
    revolutionSpeed: 0.003
  },
  {
    name: 'Neptune',
    radius: 9,
    texture: '9.jpg',
    distance: 240,
    rotationSpeed: 0.012,
    revolutionSpeed: 0.002
  }
];

const planetMaterials = planetsData.map(planet => {
  const texture = new THREE.TextureLoader().load(planet.texture);
  return new THREE.MeshPhongMaterial({ map: texture });
});
// 创建行星公转轨道
function createOrbit(radius) {
  const segments = 360;
  const material = new THREE.LineBasicMaterial({ color: 0xffffff ,linewidth: 0.1, transparent:true,opacity:0.2});
  const points = [];
  for (let i = 0; i <= segments; i++) {
    const theta = (i / segments) * Math.PI * 2;
    const x = radius * Math.cos(theta);
    const z = radius * Math.sin(theta);
    points.push(new THREE.Vector3(x, 0, z));
  }
  const geometry = new THREE.BufferGeometry().setFromPoints(points);
  const orbit = new THREE.Line(geometry, material);
  scene.add(orbit);
}

    // // 创建行星公转轨道
    // createOrbit(0.39 * 50); // 水星
    // createOrbit(0.72 * 50); // 金星
    // createOrbit(1 * 50);    // 地球
    // createOrbit(1.52 * 50); // 火星

const planets = planetMaterials.map((material, index) => {
  const planetGeometry = new THREE.SphereGeometry(planetsData[index].radius, 32, 32);
  const planet = new THREE.Mesh(planetGeometry, material);
  planet.distance = planetsData[index].distance;
  planet.rotationSpeed = planetsData[index].rotationSpeed;
  planet.revolutionSpeed = planetsData[index].revolutionSpeed;
  scene.add(planet);
  createOrbit(planetsData[index].distance)
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
    const angle = Date.now() * revolutionSpeed / 10;
    let x = Math.cos(angle) * distance;
    let z = Math.sin(angle) * distance;
    planet.position.set(x,0,z)

    // 行星自转
    planet.rotation.y += rotationSpeed;
  });
  sun.rotation.y += 0.01;

  renderer.render(scene, camera);
}

onMounted(() => {
  document.getElementById('milky').appendChild(renderer.domElement);
  animate();
});
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});
</script>

<template>
  <div id="app">
    <div id="milky"></div>
  </div>
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
