<template>
  <div class="shoe-configurator">
    <div class="canvas-container" ref="canvasContainer"></div>
    <progress
      class="progbar"
      :value="progbarValue"
      :max="progbarMax"
    ></progress>
    <div class="configurator">
      <a
        class="configurator__arrow"
        @click="
          if (currentPartIndex > 0) {
            currentPartIndex--;
            updateCameraPosition();
          } else {
            currentPartIndex = 5;
            updateCameraPosition();
          }
        "
      >
        ←
      </a>

      <div
        class="configurator__flex"
        v-if="
          (currentPartIndex && currentPartIndex < 4) || currentPartIndex == 0
        "
      >
        <div>
          <p class="configurator__subtitle" style="text-transform: capitalize">
            {{ shoePart }} ({{ currentPartIndex + 1 }}/6)
            <span v-if="shoePart === 'inside' || shoePart === 'outside'" ><br>
              (select a color and texture)</span
            >
          </p>
        </div>
        <div class="configurator__flex1">
          <div class="configurator__flex2">
            <div
              v-for="color in colorOptions"
              :key="color"
              class="configurator__options"
              @click="updateColor(shoePart, color)"
            >
              <div v-if="color == selectedColors"
                class="configurator__circle"
                :style="{ backgroundColor: color }"
              ></div>
              <div
                class="configurator__circle"
                :style="{ backgroundColor: color }"
              ></div>
            </div>
          </div>
          <div v-if="shoePart === 'inside' || shoePart === 'outside'">
            <div class="configurator__flex2">
              <div
                v-for="material in materialOptions"
                :key="material"
                class="configurator__options"
                @click="updateMaterial(materialPart, material)"
              >
                <div
                  class="configurator__circle"
                  :style="{ backgroundImage: `url(${material})` }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="configurator__flex" v-if="currentPartIndex === 4">
        <div>
          <p class="configurator__subtitle">
            Jewel ({{ currentPartIndex + 1 }}/6)
          </p>
        </div>
        <div class="configurator__flex2">
          <div
            v-for="jewelType in jewelOptions"
            :key="jewelType"
            :id="`${jewelType}jewel`"
            class="configurator__options"
            @click="updateJewel(jewelType)"
          >
            <div v-if="jewelType !== 'none'"
              class="configurator__circle"
              :style="{
                backgroundImage: `url('/media/${jewelType.toLowerCase()}.jpg')`,
                backgroundSize: 'contain',
                backgroundRepeat: 'no-repeat',
              }"
            ></div>
            <div v-else
              class="configurator__circle"
                :style="{
                  backgroundImage: `url('/media/none.png')`,
                  backgroundSize: 'contain',
                  backgroundRepeat: 'no-repeat',
                }"
            ></div>
          </div>
        </div>
      </div>

      <div class="configurator__flex" v-if="currentPartIndex === 5">
        <div>
          <p class="configurator__subtitle">
            Initials ({{ currentPartIndex + 1 }}/6)
          </p>
        </div>
        <div class="configurator__initials-container">
          <input
            class="configurator__checkbox"
            type="radio"
            id="inital-yes"
            :value="false"
            @change="initialOn"
          />
          <label for="inital-yes">yes</label>
          <div class="user-details-div" v-if="initialsState">
            <label for="initals">Initials</label>
            <input
            id="initials"
              v-model="initials"
              @input="handleInitialsInput"
              maxlength="2"
            />
          </div>
          <input
            class="configurator__checkbox"
            type="radio"
            id="inital-no"
            :value="true"
            @change="initialOff"
          />
          <label for="inital-no">no</label>
        </div>
      </div>

      <a
        class="configurator__arrow"
        @click="
          if (currentPartIndex < 5) {
            currentPartIndex++;
            updateCameraPosition();
          } else {
            currentPartIndex = 0;
            updateCameraPosition();
          }
        "
      >
        →
      </a>
    </div>

    <button v-if="progressState" class="configurator__button" @click="goToInfo">
      Next
    </button>

    <h2 class="user-details__title" v-if="progressState" ref="infoSection">
      Your information:
    </h2>
    <div v-if="progressState" class="user-details">
      <div class="user-details-div">
        <label for="shoeSize">Shoe Size</label>
        <select id="shoeSize" name="shoeSize" v-model="shoeSize">
          <option value="36">36</option>
          <option value="37">37</option>
          <option value="38">38</option>
          <option value="39">39</option>
          <option value="40">40</option>
          <option value="41">41</option>
          <option value="42">42</option>
          <option value="43">43</option>
          <option value="44">44</option>
          <option value="45">45</option>
          <option value="46">46</option>
          <option value="47">47</option>
          <option value="48">48</option>
          <option value="49">49</option>
          <option value="50">50</option>
        </select>
      </div>
      <div class="user-details-div">
        <label for="userName">Name</label>
        <input type="text" v-model="userName" />
      </div>
      <div class="user-details-div">
        <label for="userAddress">Address</label>
        <input type="text" v-model="userAddress" />
      </div>
      <div class="user-details-div">
        <label for="userEmail">Email</label>
        <input type="email" v-model="userEmail" />
      </div>
    </div>

    <div v-if="formError" class="configurator__error-message">
      {{ formError }}
    </div>
    <span v-if="orderConfirmed" class="configurator__complete-message">Your order has been sent!</span>
    <button v-if="progressState" @click="handleDoneButtonClick">
      Send order!
    </button>
  </div>
</template>

<script>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { TextureLoader } from "three/src/loaders/TextureLoader.js";
import { TextGeometry } from "three/addons/geometries/TextGeometry.js";
import { FontLoader } from "three/addons/loaders/FontLoader.js";
import { useRouter } from "vue-router";
import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader.js";
import TWEEN from "tween.js";

// const router = useRouter();

export default {
  setup() {},
  data() {
    return {
      shoeParts: ["laces", "sole", "inside", "outside"],
      materialParts: ["bottom", "top"],
      currentPartIndex: 0,
      initials: "",
      initialsState: false,
      initialsClickedOnce: false,
      selectedColors: {
        shoeColorLaces: null,
        shoeColorSole: null,
        shoeColorPanelUp: null,
        shoeColorPanelDown: null,
      },
      selectedMaterials: {
        shoeMaterialPanelUp: null,
        shoeMaterialPanelDown: null,
      },
      orderConfirmed: false,
      shoeSize: null,
      jewel: null,
      userName: null,
      userAddress: null,
      userEmail: null,
      formError: null,
      colorOptions: ["#000000", "#FFFFFF", "#FE7F00", "#69FF47"],
      materialOptions: [
        "/textures/leather.jpg",
        "/textures/holes.jpg",
        "/textures/latex.jpg",
        "/textures/fabric.jpg",
      ],
      jewelOptions: ["Giraffe", "Elephant", "Hedgehog", "Whale", "none"],
      progbarValue: 0,
      progbarMax: 8,
      progressState: false,
      socket: Primus.connect("https://dev5-sneaker-api.onrender.com/", {
          reconnect: {
          max: Infinity, // Number: The max delay before we try to reconnect.
          min: 500, // Number: The minimum delay before we try reconnect.
          retries: 10, // Number: How many times we should try to reconnect.
          },
        }),
    };
  },
  mounted() {
    const canvasContainer = this.$refs.canvasContainer;
    let windowWidth = window.innerWidth * 2;
    let windowHeight = window.innerHeight - 48;
    const ratio = windowWidth / windowHeight;

    const clock = new THREE.Clock();

    const scene = new THREE.Scene();
    scene.background = new THREE.CubeTextureLoader()
      .setPath("/cubemap/abandoned/")
      .load(["px.png", "nx.png", "py.png", "ny.png", "pz.png", "nz.png"]);

    const camera = new THREE.PerspectiveCamera(75, ratio, 0.1, 1000);

    const renderer = new THREE.WebGLRenderer();
    renderer.setPixelRatio(window.devicePixelRatio);
    canvasContainer.appendChild(renderer.domElement);

    resize();
    window.addEventListener("resize", resize);
    function resize() {
      renderer.setSize(window.innerWidth, window.innerHeight * 0.78 - 48);
      camera.aspect =
        canvasContainer.clientWidth / canvasContainer.clientHeight;
      camera.updateProjectionMatrix();

      if (window.innerWidth < 500) {
        renderer.setSize(window.innerWidth, window.innerHeight * 0.73);
        camera.aspect =
          canvasContainer.clientWidth / canvasContainer.clientHeight;
        camera.updateProjectionMatrix();
      }
    }

    camera.position.z = 9;

    const loadingManager = new THREE.LoadingManager();

    const dracoLoader = new DRACOLoader();
    dracoLoader.setDecoderPath("/draco/");
    const gltfLoader = new GLTFLoader(loadingManager);
    gltfLoader.setDRACOLoader(dracoLoader);

    const fontLoader = new FontLoader();
    const textMaterial = new THREE.MeshStandardMaterial({
      color: 0x000000,
      metalness: 0.4,
      roughness: 1,
      wireframe: true,
      wireframeLinewidth: 0.5,
    });

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.maxPolarAngle = Math.PI / 2;
    controls.enablePan = false;
    controls.minDistance = 4;
    controls.maxDistance = 10;

    const directionalLight = new THREE.DirectionalLight(0xffffff, 1.7);
    const directionalLight2 = new THREE.DirectionalLight(0xffffff, 1.7);
    const directionalLight3 = new THREE.DirectionalLight(0xffffff, 1.7);
    const directionalLight4 = new THREE.DirectionalLight(0xffffff, 1);
    directionalLight.position.set(0, 0, 1);
    directionalLight2.position.set(0, 0, -1);
    directionalLight3.position.set(0, 1, 0);
    directionalLight4.position.set(-1, 0, 0);
    scene.add(directionalLight);
    scene.add(directionalLight2);
    scene.add(directionalLight3);
    scene.add(directionalLight4);

    let shoeGroup = new THREE.Group();
    shoeGroup.rotation.order = "YXZ";

    shoeGroup.rotation.x = 0.7;
    shoeGroup.rotation.y = 0.1;

    shoeGroup.position.z = -1;
    shoeGroup.position.y = 0;
    shoeGroup.position.x = -0.5;

    let shoe;

    this.textureLoader = new TextureLoader();

    gltfLoader.load("/models/new-shoe.glb", (gltf) => {
      shoe = gltf.scene;
      shoe.scale.set(2.5, 2.5, 2.5);

      shoeGroup.add(shoe);
    });

    const resetCamera = () => {
      const initialPosition = { x: 0, y: 0, z: 9 };
      const initialRotation = { x: 0, y: 0, z: 0 };

      new TWEEN.Tween(camera.position)
        .to(initialPosition, 500)
        .easing(TWEEN.Easing.Quadratic.Out)
        .start();

      new TWEEN.Tween(camera.rotation)
        .to(initialRotation, 500)
        .easing(TWEEN.Easing.Quadratic.Out)
        .start();
    };

    const updateCameraPosition = (currentPartIndex) => {
      resetCamera();

      const targetValues = getTargetValues(this.currentPartIndex);

      new TWEEN.Tween(shoeGroup.rotation)
        .to({ x: targetValues.rotationX, y: targetValues.rotationY }, 500)
        .easing(TWEEN.Easing.Quadratic.Out)
        .start();

      new TWEEN.Tween(shoeGroup.position)
        .to({ y: targetValues.positionY, z: targetValues.positionZ }, 500)
        .easing(TWEEN.Easing.Quadratic.Out)
        .start();
    };

    const getTargetValues = (currentPartIndex) => {
      switch (this.currentPartIndex) {
        case 0:
          return {
            rotationX: 0.7,
            rotationY: 0.1,
            positionY: 0,
            positionZ: -1,
          };
        case 1:
          return {
            rotationX: 0.6,
            rotationY: 2.8,
            positionY: -0.5,
            positionZ: 1,
          };
        case 2:
          return {
            rotationX: 0.6,
            rotationY: 1,
            positionY: -0.5,
            positionZ: -1,
          };
        case 3:
          return {
            rotationX: 0.6,
            rotationY: -0.8,
            positionY: -0.5,
            positionZ: -1,
          };
        case 4:
          return {
            rotationX: 0.6,
            rotationY: 1.5,
            positionY: -0.5,
            positionZ: 1.6,
          };
        case 5:
          return {
            rotationX: -0.2,
            rotationY: 3.3,
            positionY: -0.5,
            positionZ: 1.2,
          };
        default:
          return {
            rotationX: 0.7,
            rotationY: 0.1,
            positionY: 0,
            positionZ: -1,
          };
      }
    };

    this.updateCameraPosition = updateCameraPosition;

    const jewelModels = {
      Giraffe: { model: null, position: new THREE.Vector3(-1.35, 0.8, -1.25) },
      Elephant: { model: null, position: new THREE.Vector3(-1.2, 1.2, -1.25) },
      Hedgehog: { model: null, position: new THREE.Vector3(-1.15, 1.4, -1.3) },
      Whale: { model: null, position: new THREE.Vector3(-1, 1.5, -1.25) },
      none: { model: null, position: new THREE.Vector3(-1, 1.5, -1.25) },
    };

    Object.keys(jewelModels).forEach((jewelType) => {
      if (jewelType !== "none") {
        const modelPath = `/models/pendant${jewelType}.glb`;
        gltfLoader.load(modelPath, (gltf) => {
          const jewelModel = gltf.scene;
          jewelModel.scale.set(0.05, 0.05, 0.05);
          jewelModel.rotation.x = -2;
          jewelModel.rotation.y = 0.4;
          jewelModel.rotation.z = -1.7;
          jewelModel.position.copy(jewelModels[jewelType].position);
  
          const material = new THREE.MeshStandardMaterial({
            color: 0xffd700,
            metalness: 1,
            roughness: 0.3,
          });
  
          jewelModel.traverse((child) => {
            if (child instanceof THREE.Mesh) {
              child.material = material;
            }
          });
  
          jewelModel.visible = false;
          jewelModels[jewelType].model = jewelModel;
          shoeGroup.add(jewelModel);
        });
      }
    });

    const updateJewel = (jewelType) => {
        handleProgress("jewel");
        Object.keys(jewelModels).forEach((type) => {
          if (jewelModels[type].model) {
            const model = jewelModels[type].model;
            model.visible = type === jewelType;
            if (type === jewelType) this.jewel = jewelType;
          }
        });
    };

    this.updateJewel = updateJewel;

    const updateColor = (colorType, hexColor) => {
      if (shoe) {
        let material;
        switch (colorType) {
          case "laces":
            handleProgress("laces");
            material = shoe.getObjectByName("laces").material;
            this.selectedColors.shoeColorLaces = hexColor;
            break;
          case "sole":
            handleProgress("sole");
            const soleMaterialTop = shoe.getObjectByName("sole_1").material;
            const soleMaterialBottom = shoe.getObjectByName("sole_2").material;
            soleMaterialTop.color.setStyle(hexColor);
            soleMaterialTop.needsUpdate = true;
            soleMaterialBottom.color.setStyle(hexColor);
            soleMaterialBottom.needsUpdate = true;
            this.selectedColors.shoeColorSole = hexColor;
            break;
          case "inside":
            handleProgress("inside");
            material = shoe.getObjectByName("inside").material;
            this.selectedColors.shoeColorPanelDown = hexColor;
            break;
          case "outside":
            handleProgress("outside");
            const topMaterialTop = shoe.getObjectByName("outside_1").material;
            const topMaterialBottom =
              shoe.getObjectByName("outside_2").material;
            topMaterialTop.color.setStyle(hexColor);
            topMaterialTop.needsUpdate = true;
            topMaterialBottom.color.setStyle(hexColor);
            topMaterialBottom.needsUpdate = true;
            this.selectedColors.shoeColorPanelUp = hexColor;
            break;
          default:
            break;
        }

        if (material) {
          material.color.setStyle(hexColor);
          material.needsUpdate = true;
        }
      }
    };

    this.updateColor = updateColor;

    const updateMaterial = (materialType, textureUrl) => {
      if (shoe) {
        const texture = this.textureLoader.load(textureUrl);

        texture.repeat.set(2, 2);
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;

        let material;
        switch (materialType) {
          case "top":
            handleProgress("top");
            material = shoe.getObjectByName("outside_1").material;
            this.selectedMaterials.shoeMaterialPanelUp = textureUrl;
            break;
          case "bottom":
            handleProgress("bottom");
            material = shoe.getObjectByName("inside").material;
            this.selectedMaterials.shoeMaterialPanelDown = textureUrl;
            break;
          default:
            break;
        }

        if (material) {
          material.map = texture;
          material.needsUpdate = true;
        }
      }
    };

    this.updateMaterial = updateMaterial;

    const animate = () => {
      requestAnimationFrame(animate);
      TWEEN.update();
      renderer.render(scene, camera);
      shoeGroup.position.y = Math.sin(clock.getElapsedTime()) * 0.2 - 0.8;
    };

    animate();

    const handleInitialsInput = () => {
      this.initials = this.initials.toUpperCase();
    };

    this.handleInitialsInput = handleInitialsInput;

    const initialOn = () => {
      this.initialsState = true;
      toggleInitials();
      document.getElementById("inital-no").checked = false;
    };

    this.initialOn = initialOn;

    const initialOff = () => {
      this.initialsState = false;
      toggleInitials();
      document.getElementById("inital-yes").checked = false;
    };

    this.initialOff = initialOff;

    const toggleInitials = () => {
      handleProgress("initials");

      if (this.initialsState === true) {
        fontLoader.load("fonts/helvetiker_regular.typeface.json", (font) => {
          const textGeometry = new TextGeometry(this.initials, {
            font: font,
            size: 0.25,
            height: 0.01,
            curveSegments: 12,
            bevelEnabled: true,
            bevelThickness: 0.03,
            bevelSize: 0.02,
            bevelOffset: 0,
            bevelSegments: 5,
          });

          this.shoeText = new THREE.Mesh(textGeometry, textMaterial);

          this.shoeText.rotation.order = "YXZ";

          this.shoeText.rotation.x = 0.15;
          this.shoeText.rotation.y = -3.3;

          this.shoeText.position.x = 0.4;
          this.shoeText.position.y = 1.8;
          this.shoeText.position.z = -2.5;

          shoeGroup.add(this.shoeText);
        });
      } else if (this.initialsState === false) {
        shoeGroup.remove(this.shoeText);
      }
    };

    this.toggleInitials = toggleInitials;

    const handleProgress = (selectedItem) => {
      switch (selectedItem) {
        case "laces":
          if (this.selectedColors.shoeColorLaces === null) {
            this.progbarValue += 1;
          }
          break;
        case "sole":
          if (this.selectedColors.shoeColorSole === null) {
            this.progbarValue += 1;
          }
          break;
        case "inside":
          if (this.selectedColors.shoeColorPanelDown === null) {
            this.progbarValue += 1;
          }
          break;
        case "outside":
          if (this.selectedColors.shoeColorPanelUp === null) {
            this.progbarValue += 1;
          }
          break;
        case "top":
          if (this.selectedMaterials.shoeMaterialPanelUp === null) {
            this.progbarValue += 1;
          }
          break;
        case "bottom":
          if (this.selectedMaterials.shoeMaterialPanelDown === null) {
            this.progbarValue += 1;
          }
          break;
        case "jewel":
          if (this.jewel === null) {
            this.progbarValue += 1;
          }
          break;
        case "initials":
          if (this.initialsClickedOnce === false) {
            this.progbarValue += 1;
            this.initialsClickedOnce = true;
          }
          break;
        default:
          break;
      }

      if (this.progbarValue === this.progbarMax && !this.progressState) {
        this.progressState = true;
      }
    };

    this.handleProgress = handleProgress;

    scene.add(shoeGroup);

    fetch("https://dev5-sneaker-api.onrender.com/api/v1/users", {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            this.userName = data.data[0].userName;
            this.userEmail = data.data[0].email;
          } else {
            console.error("Something went wrong!");
          }
        });
        
  },

  methods: {
    goToInfo() {
      this.$refs.infoSection.scrollIntoView({ behavior: "smooth" });
    },
    updateColor(type, hexColor) {
      if (shoe) {
        const material = shoe.getObjectByName(type).material;
        material.color.setStyle(hexColor);
        material.needsUpdate = true;
        this.selectedColors[type] = hexColor;
      }
    },
    handleDoneButtonClick() {
      if (
        this.shoeSize &&
        this.userName &&
        this.userAddress &&
        this.userEmail &&
        this.selectedColors.shoeColorLaces &&
        this.selectedColors.shoeColorSole &&
        this.selectedColors.shoeColorPanelDown &&
        this.selectedColors.shoeColorPanelUp &&
        this.selectedMaterials.shoeMaterialPanelDown &&
        this.selectedMaterials.shoeMaterialPanelUp
      ) {
        this.formError = null;

        this.fetchData();
      } else {
        this.formError =
          "Please fill in all the required fields and selections.";
      }
    },

    fetchData() {
      if (this.initialsState === false) {
        this.initials = "";
      }

      const data = {
        shoe: {
          shoeType: "AIR REV. NITRO S",
          shoeSize: this.shoeSize,
          shoeColorSole: this.selectedColors.shoeColorSole,
          shoeColorLaces: this.selectedColors.shoeColorLaces,
          shoeColorPanelDown: this.selectedColors.shoeColorPanelDown,
          shoeColorPanelUp: this.selectedColors.shoeColorPanelUp,
          shoeMaterialPanelDown: this.selectedMaterials.shoeMaterialPanelDown,
          shoeMaterialPanelUp: this.selectedMaterials.shoeMaterialPanelUp,
          jewel: this.jewel || "none",
          initials: this.initials,
          status: "Order placed",
          userName: this.userName,
          userAddress: this.userAddress,
          userEmail: this.userEmail,
        },
      };

      data.shoe.colorOptions = this.colorOptions;
      data.shoe.selectedColors = this.selectedColors;
      data.shoe.selectedMaterials = this.selectedMaterials;

      fetch("https://dev5-sneaker-api.onrender.com/api/v1/shoes", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(data),
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            this.socket.write({
              "action": "create",
            });
            if (localStorage.getItem("token")) {
              window.location.href = "/overview";
            } else {
              this.orderConfirmed = true;
            }
          } else {
            console.error(data);
          }
        });
    },
  },

  computed: {
    shoePart() {
      return this.shoeParts[this.currentPartIndex];
    },
    materialPart() {
      return this.materialParts[this.currentPartIndex - 2];
    },
  },
};
</script>
