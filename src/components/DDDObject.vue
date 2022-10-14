<template>
  <div>
    <canvas class="w-full" id="three" />
  </div>
</template>

<script setup lang="ts">
import { onMounted } from "vue";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { createConditionalExpression } from "@vue/compiler-core";

onMounted(() => {
  initThree();
});

const initThree = () => {
  const scene = new THREE.Scene();
  const canvas = document.querySelector("#three") as HTMLCanvasElement;
  const renderer = new THREE.WebGLRenderer({
    canvas,
    antialias: true,
    alpha: true,
  });
  const camera = new THREE.PerspectiveCamera(
    50,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  );
  camera.position.x = 3;
  camera.position.z = 3;

  function animate() {
    renderer.render(scene, camera);
    requestAnimationFrame(animate);
  }
  animate();

  const gltfLoader = new GLTFLoader();
  console.log(`${import.meta.env.BASE_URL}shiba/scene.gltf`)
  gltfLoader.load(`${import.meta.env.BASE_URL}/shiba/scene.gltf`, (gltf) => {
    let model = gltf.scene;

    //添加这段代码
    //遍历模型每部分
    model.traverse((o) => {
      //将图片作为纹理加载
      let explosionTexture = new THREE.TextureLoader().load(
        `${import.meta.env.BASE_URL}shiba/textures/default_baseColor.png`
      );
      //调整纹理图的方向
      explosionTexture.flipY = false;
      //将纹理图生成基础网格材质(MeshBasicMaterial)
      const material = new THREE.MeshBasicMaterial({
        map: explosionTexture,
      });
      //给模型每部分上材质
      (o as THREE.Mesh).material = material;
    });

    scene.add(model);

    const hemLight = new THREE.HemisphereLight(0xffffff, 0xffffff, 0.6);
    hemLight.position.set(0, 48, 0);
    scene.add(hemLight);

    const dirLight = new THREE.DirectionalLight(0xffffff, 0.6);
    //光源等位置
    dirLight.position.set(-10, 8, -5);
    //可以产生阴影
    dirLight.castShadow = true;
    dirLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
    scene.add(dirLight);

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;

    const resizeRendererToDisplaySize = (renderer: THREE.WebGLRenderer) => {
      const canvas = renderer.domElement;
      var width = window.innerWidth;
      var height = window.innerHeight;
      var canvasPixelWidth = canvas.width / window.devicePixelRatio;
      var canvasPixelHeight = canvas.height / window.devicePixelRatio;

      const needResize =
        canvasPixelWidth !== width || canvasPixelHeight !== height;
      if (needResize) {
        renderer.setSize(width, height, false);
      }
      return needResize;
    };

    const animate = () => {
      controls.update();
      renderer.render(scene, camera);
      requestAnimationFrame(animate);

      if (resizeRendererToDisplaySize(renderer)) {
        const canvas = renderer.domElement;
        camera.aspect = canvas.clientWidth / canvas.clientHeight;
        camera.updateProjectionMatrix();
      }
    };
    animate();
  });
};
</script>
