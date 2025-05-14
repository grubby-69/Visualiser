# Visualiser
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>3D Female Body Visualizer</title>
  <style>
    body { font-family: Arial; margin: 0; display: flex; }
    #controls {
      width: 300px;
      padding: 20px;
      background: #f0f0f0;
    }
    label { display: block; margin: 10px 0 5px; }
    input[type=range] { width: 100%; }
    canvas { display: block; }
  </style>
</head>
<body>
  <div id="controls">
    <h2>Adjust Measurements</h2>
    <label>Bust (cm): <span id="bustVal">90</span></label>
    <input type="range" min="70" max="130" value="90" id="bust">

    <label>Waist (cm): <span id="waistVal">70</span></label>
    <input type="range" min="50" max="110" value="70" id="waist">

    <label>Hips (cm): <span id="hipsVal">100</span></label>
    <input type="range" min="70" max="140" value="100" id="hips">

    <label>Shoulders (cm): <span id="shouldersVal">40</span></label>
    <input type="range" min="30" max="60" value="40" id="shoulders">

    <label>Height (cm): <span id="heightVal">170</span></label>
    <input type="range" min="140" max="200" value="170" id="height">
  </div>
  <div id="renderer" style="flex-grow: 1;"></div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r152/three.min.js"></script>
  <script>
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0xfdfdfd);
    const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.z = 5;

    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth - 300, window.innerHeight);
    document.getElementById('renderer').appendChild(renderer.domElement);

    const light = new THREE.DirectionalLight(0xffffff, 1);
    light.position.set(1, 2, 3);
    scene.add(light);
    scene.add(new THREE.AmbientLight(0xffffff, 0.6));

    const material = new THREE.MeshStandardMaterial({ color: 0xffb6c1, flatShading: true });

    const torso = new THREE.Mesh(new THREE.CylinderGeometry(1, 1.2, 2.5, 32), material);
    scene.add(torso);

    const head = new THREE.Mesh(new THREE.SphereGeometry(0.4, 32, 32), new THREE.MeshStandardMaterial({ color: 0xffe0bd }));
    head.position.y = 1.6;
    scene.add(head);

    function updateBody() {
      const bust = +bustSlider.value;
      const waist = +waistSlider.value;
      const hips = +hipsSlider.value;
      const shoulders = +shouldersSlider.value;
      const height = +heightSlider.value;

      bustVal.textContent = bust;
      waistVal.textContent = waist;
      hipsVal.textContent = hips;
      shouldersVal.textContent = shoulders;
      heightVal.textContent = height;

      const heightScale = (height - 140) / 60 + 1;
      const topRadius = shoulders / 100;
      const midRadius = waist / 100;
      const bottomRadius = hips / 100;

      scene.remove(torso);
      torso.geometry.dispose();

      torso.geometry = new THREE.CylinderGeometry(midRadius, bottomRadius, 2.5 * heightScale, 32, 1);
      torso.scale.x = (bust / 100 + shoulders / 100) / 2;
      torso.scale.z = torso.scale.x;
      torso.position.y = 0;

      head.position.y = 2.5 * heightScale / 2 + 0.4;
    }

    // Get sliders
    const bustSlider = document.getElementById('bust');
    const waistSlider = document.getElementById('waist');
    const hipsSlider = document.getElementById('hips');
    const shouldersSlider = document.getElementById('shoulders');
    const heightSlider = document.getElementById('height');

    const bustVal = document.getElementById('bustVal');
    const waistVal = document.getElementById('waistVal');
    const hipsVal = document.getElementById('hipsVal');
    const shouldersVal = document.getElementById('shouldersVal');
    const heightVal = document.getElementById('heightVal');

    bustSlider.oninput = waistSlider.oninput = hipsSlider.oninput = shouldersSlider.oninput = heightSlider.oninput = updateBody;

    updateBody();

    function animate() {
      requestAnimationFrame(animate);
      torso.rotation.y += 0.005;
      renderer.render(scene, camera);
    }
    animate();
  </script>
</body>
</html>
