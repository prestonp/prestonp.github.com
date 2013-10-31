---
layout: post
title: threejs hello world
category: posts
---
<style>
  canvas {
    height: 100%;
    width: 100%;
  }
</style>

<script src="https://rawgithub.com/mrdoob/three.js/master/build/three.js"></script>
<script>
  // Scene, camera, renderer
  var scene = new THREE.Scene();
  var fov = 75;
  var aspectRatio = window.innerWidth / window.innerHeight;
  var camera = new THREE.PerspectiveCamera( fov, aspectRatio, 0.1, 1000 );
  var renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  // Geometry
  var geometry = new THREE.CubeGeometry(1,1,1);
  var material = new THREE.MeshLambertMaterial( { color: 0x00ff00 } );
  var cube = new THREE.Mesh( geometry, material );
  scene.add( cube );

  renderer.setClearColor(0x333F47, 1);

  // Create a light, set its position, and add it to the scene.
  var light = new THREE.PointLight(0xffffff);
  light.position.set(-100,200,100);
  scene.add(light);



  camera.position.z = 5;

  // Render Loop
  function render() {
    requestAnimationFrame(render);
    cube.rotation.x += 0.01;
    cube.rotation.y += 0.01;
    renderer.render(scene, camera);
  }
  render();
</script>
