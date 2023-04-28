# webglborgattack
A 3d game where you have to navigate through an attack from missile fired from Borg Cube
Navigate through the heavy missile attack from borg cube.

Html file:
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Space Shooter</title>
    <style>
      body {
        margin: 0;
      }
      canvas {
        display: block;
      }
      #info {
        position: absolute;
        top: 10px;
        left: 10px;
        color: white;
        font-family: Arial, sans-serif;
      }
      #sliderContainer {
        position: absolute;
        bottom: 10px;
        left: 10px;
      }
      #sliderLabel {
        color: white;
        font-family: Arial, sans-serif;
      }
    </style>
  </head>
  <body>
    <div id="info">
      <p id="health">Health: 100</p>
      <p id="stoppedMissiles">Stopped Missiles: 0</p>
    </div>
    <div id="sliderContainer">
      <label id="sliderLabel" for="missileSlider">Missiles: 5</label>
      <input type="range" id="missileSlider" min="1" max="50" value="15" />
    </div>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="main2.js"></script>
  </body>
</html>


javascript: main2.js

// Set up the scene, camera, and renderer
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
// Create a starry space background
const spaceTextureLoader = new THREE.TextureLoader();
const spaceTexture = spaceTextureLoader.load('space.jpg');
spaceTexture.wrapS = THREE.RepeatWrapping;
spaceTexture.wrapT = THREE.RepeatWrapping;
spaceTexture.repeat.set(1, 1);

const spaceGeometry = new THREE.PlaneGeometry(40, 40);
const spaceMaterial = new THREE.MeshBasicMaterial({ map: spaceTexture, side: THREE.DoubleSide });
const spaceBackground = new THREE.Mesh(spaceGeometry, spaceMaterial);
spaceBackground.position.z = -15;
scene.add(spaceBackground);
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// Create the spaceship
const spaceshipGeometry = new THREE.BoxGeometry();
const spaceshipMaterial = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const spaceship = new THREE.Mesh(spaceshipGeometry, spaceshipMaterial);
scene.add(spaceship);

// Set the camera position
camera.position.z = 5;

// Set up the controls
const controls = {
  up: false,
  down: false,
  left: false,
  right: false,
};

// Handle keydown events
document.addEventListener("keydown", (e) => {
  if (e.code === "ArrowUp") controls.up = true;
  if (e.code === "ArrowDown") controls.down = true;
  if (e.code === "ArrowLeft") controls.left = true;
  if (e.code === "ArrowRight") controls.right = true;
});

// Handle keyup events
document.addEventListener("keyup", (e) => {
  if (e.code === "ArrowUp") controls.up = false;
  if (e.code === "ArrowDown") controls.down = false;
  if (e.code === "ArrowLeft") controls.left = false;
  if (e.code === "ArrowRight") controls.right = false;
});

// Create the missile
const missileCylinderGeometry = new THREE.CylinderGeometry(0.2, 0.2, 1, 32);
const missileConeGeometry = new THREE.ConeGeometry(0.2, 0.5, 32);
const missileMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 });

const missileCylinder = new THREE.Mesh(missileCylinderGeometry, missileMaterial);
const missileCone = new THREE.Mesh(missileConeGeometry, missileMaterial);
const missile = new THREE.Group();
missile.add(missileCylinder);
missile.add(missileCone);
scene.add(missile);

missileCone.position.y = 0.75;
missile.position.set(Math.random() * 10 - 5, Math.random() * 10 - 5, -10);
let health = 100;
let stoppedMissiles = 0;
// Create multiple missiles
const numMissiles = 45;
const missiles = [];

for (let i = 0; i < numMissiles; i++) {
  const missileCylinderGeometry = new THREE.CylinderGeometry(0.2, 0.2, 1, 32);
  const missileConeGeometry = new THREE.ConeGeometry(0.2, 0.5, 32);
  const missileMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 });

  const missileCylinder = new THREE.Mesh(missileCylinderGeometry, missileMaterial);
  const missileCone = new THREE.Mesh(missileConeGeometry, missileMaterial);
  const missile = new THREE.Group();
  missile.add(missileCylinder);
  missile.add(missileCone);
  scene.add(missile);

  missileCone.position.y = 0.75;
  missile.position.set(Math.random() * 10 - 5, Math.random() * 10 - 5, -10 - 5 * i);
  missile.rotation.x = Math.PI / 2;

  missiles.push(missile);
}
// Update the health and stopped missiles display
function updateInfo() {
  document.getElementById('health').innerHTML = `Health: ${health}`;
  document.getElementById('stoppedMissiles').innerHTML = `Stopped Missiles: ${stoppedMissiles}`;
}

// Rotate the missile to be parallel to the Z-axis
missile.rotation.x = Math.PI / 2;
// Check for collision between two objects
function isColliding(object1, object2, radius1, radius2) {
    const distance = object1.position.distanceTo(object2.position);
    return distance < (radius1 + radius2);
  }
// Listen for slider input and update the label
const missileSlider = document.getElementById('missileSlider');
const sliderLabel = document.getElementById('sliderLabel');

missileSlider.addEventListener('input', function () {
  const numMissiles = parseInt(missileSlider.value);
  sliderLabel.textContent = `Missiles: ${numMissiles}`;

  // Show or hide missiles based on slider value
  for (let i = 0; i < maxMissiles; i++) {
    if (i < numMissiles) {
      missiles[i].visible = true;
    } else {
      missiles[i].visible = false;
    }
  }
});

// Update the game loop
function update() {
    if (health <= 0) {
      // Stop the game when health reaches 0
      alert('Game Over!');
      return;
    }
  
    requestAnimationFrame(update);

  // Control the spaceship
  if (controls.up) spaceship.position.y += 0.1;
  if (controls.down) spaceship.position.y -= 0.1;
  if (controls.left) spaceship.position.x -= 0.1;
  if (controls.right) spaceship.position.x += 0.1;

  // Move the missiles and check for collisions
  missiles.forEach((missile) => {
    // Move the missile
    missile.position.z += 0.1;

    // Check for collision between the spaceship and the missile
    const spaceshipRadius = 0.5; // half of spaceship box size
    const missileRadius = 0.2; // radius of missile's cylinder
    if (isColliding(spaceship, missile, spaceshipRadius, missileRadius)) {
      // Respawn the missile at a new position
      missile.position.set(Math.random() * 10 - 5, Math.random() * 10 - 5, -10);
      missile.rotation.x = Math.PI / 2;

      // Decrease health and update the display
      health -= 10;
      updateInfo();
    }

    // If the missile is out of view, respawn it and update the stopped missiles count
    if (missile.position.z > 10) {
      missile.position.set(Math.random() * 10 - 5, Math.random() * 10 - 5, -10);
      missile.rotation.x = Math.PI / 2;

      stoppedMissiles++;
      updateInfo();
    }
  });

  // Render the scene
  renderer.render(scene, camera);
}

// Start the game loop
update();


//please make sure you have a proper jpg file for borg background. Also run the game within server in order for texture to work properly
