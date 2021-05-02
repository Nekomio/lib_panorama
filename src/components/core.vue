<template>
  <div>
    <!--SelectScene-->
    <div v-if="!selectScene" class="select" v-on:click="selectScene=true">切换场景</div>
    <div v-if="selectScene" class="web">
      <h2 class="byr-main-title">北京邮电大学图书馆</h2>
      <h3 class="byr-sub-title">下拉菜单可跳转场景</h3>
      <!--    实现首页的手风琴-->
      <div class="accordion">
        <div class="tab">
          <label for="tab1">
            <div class="tab-item">楼层1</div>
          </label>
          <input type="radio" name="tab" id="tab1" />
          <div class="tab-content-item">
            <!-- <div
              v-for="item in getSceneNames()"
              :key="item"
              class="selectTag"
              v-on:click="jumpTo({detail:{dest:item}});selectScene=false"
            >{{item}}</div> -->
            <ul data-index="1">
              <li v-for="item in getSceneNames(1)"
                  :key="item"><div v-on:click="jumpTo({detail:{dest:item}});selectScene=false" class="byr-floor-room">{{item}}</div></li>
              <!-- <li data-requestId="p16"><div class="byr-floor-room" >入口刷卡处</div></li>
              <li data-requestId="p16"><div class="byr-floor-room" >入口屏幕处</div></li> -->
            </ul>
          </div>
          <label for="tab2">
            <div class="tab-item">楼层2</div>
          </label>
          <input type="radio" name="tab" id="tab2" />
          <div class="tab-content-item">
            <ul data-index="2">
              <li v-for="item in getSceneNames(2)"
                  :key="item"><div v-on:click="jumpTo({detail:{dest:item}});selectScene=false" class="byr-floor-room">{{item}}</div></li>

            </ul>
          </div>
          <label for="tab3">
            <div class="tab-item">楼层3</div>
          </label>
          <input type="radio" name="tab" id="tab3" />
          <div class="tab-content-item">
            <ul data-index="3">
              <li v-for="item in getSceneNames(3)"
                  :key="item"><div v-on:click="jumpTo({detail:{dest:item}});selectScene=false" class="byr-floor-room">{{item}}</div></li>
            </ul>
          </div>
          <label for="tab4">
            <div class="tab-item">楼层4</div>
          </label>
          <input type="radio" name="tab" id="tab4" />
          <div class="tab-content-item">
            <ul data-index="4">
              <li v-for="item in getSceneNames(4)"
                  :key="item"><div v-on:click="jumpTo({detail:{dest:item}});selectScene=false" class="byr-floor-room">{{item}}</div></li>
            </ul>
          </div>
        </div>
        <!-- <div class="floor_controller">
          <i class="floor_up" title="上楼">
            <img src="https://vr.nekomio.com/file/VR/up.png" id="up" />
          </i>
          <i class="floor_down" title="下楼">
            <img src="https://vr.nekomio.com/file/VR/down.png" id="down" />
          </i>
        </div> -->
        <div>
          <!--        点赞功能开始-->
          <!-- <div class="love" title="点赞">
            <span class="count">10</span>
            <img src="images/love.jpg" id="love" />
          </div> -->
          <!--        点赞功能结束-->
          <!-- <i class="message" title="交流">
            <a href="html/message.html" target="_blank">
              <img src="images/message.jpg" id="message" /> -->
            <!-- </a> -->
          <!-- </i> -->
        </div>
      </div>
      <!-- <div
        v-for="item in getSceneNames()"
        :key="item"
        class="selectTag"
        v-on:click="jumpTo({detail:{dest:item}});selectScene=false"
      >{{item}}</div> -->
    </div>
    <div v-if="selectScene" class="select" v-on:click="selectScene=false">取消</div>
    <div v-if="editorAddIcon" class="editorPanel">
      <!-- ADD ICON-->
      <button v-on:click="edAddIcon()">确认</button>
      <button v-on:click="edIconMode=''; edIconContent=''; editorAddIcon=false">取消</button>
      <br />
      <select v-model="edIconMode">
        <option disabled value>选择图标类型</option>
        <option>jump</option>
        <option>audio</option>
        <option>text</option>
      </select>
      <br />
      <select v-if="edIconMode=='jump'" v-model="edIconContent" user-scalable="no">
        <option disabled value>选择场景</option>
        <option v-for="item in getSceneNames()" :key="item">{{item}}</option>
      </select>
      <input v-if="edIconMode=='audio'" v-model="edIconContent" placeholder="请输入音频链接" />
      <textarea v-if="edIconMode=='text'" v-model="edIconContent" placeholder="请输入文字介绍" />
      <div>
        位置
        <br />
        X{{editCenter.position.x}}
        <br />
        Y{{editCenter.position.y}}
        <br />
        Z{{editCenter.position.z}}
      </div>
    </div>
    <!-- ADD SCENE-->
    <div v-if="editorAddScene" class="editorPanel">
      <button v-on:click="edAddScene()">确认</button>
      <button v-on:click="edSceneName=''; editorAddScene=false">取消</button>
      <br />
      <input v-model="edSceneName" placeholder="请输入场景名称" />
      <input v-model="edSceneFloor" placeholder="层数" />
      <input type="file" id="upload" />
    </div>
    <!--Delete Scene-->
    <div v-if="editorDelScene" class="editorPanel">
      <button v-on:click="edDelScene()">确认</button>
      <button v-on:click="edSceneName=''; editorDelScene=false">取消</button>
      <br />
      <select v-model="edSceneName">
        <option disabled value>选择场景</option>
        <option v-for="item in getSceneNames()" :key="item">{{item}}</option>
      </select>
    </div>
    <!--TextDesc-->
    <div v-if="textDesc" id="textdesc">
      <br />
      {{textDescContent}}
      <div v-on:click="textDesc=false" id="textdescbtn">关闭</div>
    </div>
    <!--Scene-->
    <div id="sceneContainer" width="100%" height="100%">
      <button id="modebutton" v-if="touchMode" v-on:click="touchMode=false">切换至重力感应</button>
      <button id="modebutton2" v-else v-on:click="touchMode=true">恢复手动控制</button>
      <!-- <button class="edit" v-if="!editMode&&mode=='edit'" v-on:click="editModeOn()">开启编辑模式</button> -->
      <button class="edit" v-if="editMode" v-on:click="editModeOff()">关闭编辑模式</button>
      <div id="editbar" v-if="editMode">
        <button v-on:click="editorAddIcon=true">添加图标</button>
        <button v-on:click="edDelIcon()">删除图标</button>
        <button v-on:click="editorAddScene=true">添加场景</button>
        <button v-on:click="editorDelScene=true">删除场景</button>
      </div>
      <audio id="desc" />
    </div>
  </div>
</template> 

<script>
import * as THREE from "three";
import { Matrix4, PlaneGeometry } from "three";
import orientHandler from "./scripts/orienter.js";
import sceneObj from "./scripts/scene";
import { log } from "util";
import { Promise, reject } from "q";
export default {
  name: "HelloWorld",
  data() {
    return {
      bigGeom: new THREE.PlaneGeometry(2, 2, 10, 10),
      smallGeom: new THREE.PlaneGeometry(1, 1, 10, 10),
      jumpTex: null,
      textTex: null,
      audioTex: null,
      jumpMat: null,
      textMat: null,
      audioMat: null,
      mode: "",
      token: "",
      floor: 1,
      editMode: false,
      selectScene: false,
      editCenter: {},
      editorAddIcon: false,
      editorAddScene: false,
      editorDelScene: false,
      edSceneName: "",
      edSceneFloor: "",
      edIconContent: "",
      edIconMode: "",
      textDesc: false,
      textDescContent: "",
      sceneMap: {},
      currentScene: "",
      skyBoxPartialOK: [false, false, false, false],
      skyBoxMat: {},
      skyBoxTexture: {},
      skyBoxGeom: {},
      touchMode: true,
      skyboxReady: false,
      camera: null,
      scene: null,
      renderer: null,
      raycaster: null,
      skyBoxMesh: 0,
      mouseDown: false,
      touchDown: false,
      mouseSpeed: 0.1,
      touchSpeed: 0.3,
      pre: { x: 0, y: 0 },
      eularAngle: { x: 0, y: 0 },
      deviceOrientationData: {
        alpha: 0,
        beta: 0,
        gamma: 0
      },
      currentScreenOrientation: 0,
      orientH: null
    };
  },
  props: {
    msg: String
  },
  methods: {
    /*
    editModeOn: function() {
      this.editMode = true;
      this.scene.add(this.editCenter);
    },
    editModeOff: function() {
      let ret = [];
      for (let name of this.sceneMap.keys()) {
        ret.push({
          key: name,
          value: this.sceneMap.get(name).stringify()
        });
      }
      let retstr = JSON.stringify({ scenes: ret, mode: this.mode });
      let retblob = new Blob([retstr], { type: "text/plain" });
      let file = new File([retblob], "vrconfig.txt");
      this.$axios.post(
        `https://vr.nekomio.com/admin/api/resources/VR/${file.name}?override=true`,
        file.slice(),
        {
          headers: { "X-Auth": this.token, "Content-Type": "text/html" }
        }
      );
      this.editMode = false;
      this.scene.remove(this.editCenter);
    },*/
    getSceneNames: function(id = -1) {
      let ret = [];
      for (let name of this.sceneMap.keys()) {
        console.log(this.sceneMap.get(name));
        if (id != -1 && id != this.sceneMap.get(name).floor) continue;
        ret.push(name);
      }
      return ret;
    },/*
    edAddIcon: function() {
      let pos = {
        theta: THREE.Math.degToRad(this.eularAngle.x),
        row: 10,
        h: this.editCenter.position.y
      };
      if (this.edIconMode == "jump") {
        let cur = this.sceneMap.get(this.currentScene);
        let obj = cur.addJumpObj(
          this.getButton("jump"),
          this.edIconContent,
          pos
        );
        this.scene.add(obj);
      } else {
        let cur = this.sceneMap.get(this.currentScene);
        let obj = cur.addDescObj(
          this.getButton(this.edIconMode),
          this.edIconContent,
          this.edIconMode,
          pos
        );
        this.scene.add(obj);
      }
      this.edIconMode = "";
      this.edIconContent = "";
      this.editorAddIcon = false;
    },
    edDelIcon: function() {
      event.preventDefault();
      let ry = new THREE.Vector2(0, 0);
      this.raycaster.setFromCamera(ry, this.camera);
      let intersections = this.raycaster.intersectObjects(this.scene.children);
      for (let obj of intersections) {
        if (!(obj.object.callbk === undefined))
          obj.object.callbk(this.scene, true);
      }
    },
    edAddScene: async function() {
      this.editorAddScene = false;
      let file = document.getElementById("upload").files[0];
      if (!file || this.edSceneName.length == 0) {
        alert("输入无效，场景添加失败");
        return;
      }
      let img = await new Promise((resolve, reject) => {
        let reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = e => {
          resolve(e.target.result);
        };
      });
      let compressedimg = await this.compressMap(img);
      let cutimg = await this.cutMap(img);
      let mini = await new Promise((resolve, reject) => {
        this.$axios
          .post(
            `https://vr.nekomio.com/admin/api/resources/VR/${compressedimg.name}?override=true`,
            compressedimg.slice(),
            {
              headers: { "X-Auth": this.token, "Content-Type": "text/html" }
            }
          )
          .then(
            resolve(`https://vr.nekomio.com/file/VR/${compressedimg.name}`)
          );
      });
      let partial = [];
      for (let cimg of cutimg) {
        let pt = await new Promise((resolve, reject) => {
          this.$axios
            .post(
              `https://vr.nekomio.com/admin/api/resources/VR/${cimg.name}?override=true`,
              cimg.slice(),
              {
                headers: { "X-Auth": this.token, "Content-Type": "text/html" }
              }
            )
            .then(resolve(`https://vr.nekomio.com/file/VR/${cimg.name}`));
        });
        partial.push(pt);
        //console.log(pt);
      }
      let sc = new sceneObj(this.edSceneName, mini, partial, parseInt(this.edSceneFloor));
      this.sceneMap.set(sc.name, sc);
      this.edSceneName = "";
    },
    edDelScene: function() {
      this.sceneMap.delete(this.edSceneName);
      this.edSceneName = "";
      //console.log(this.edSceneName, this.sceneMap);
      this.editorDelScene = false;
    },
    compressMap: function(src) {
      return new Promise((resolve, reject) => {
        let img = new Image();
        img.src = src;
        img.onload = () => {
          let canvast = document.createElement("canvas");
          let ctx = canvast.getContext("2d");
          //(img.width = 4096), (img.height = 2048);
          (canvast.width = 4096), (canvast.height = 2048);
          ctx.drawImage(img, 0, 0, 4096, 2048);
          let retblob = this.dataURLtoBlob(
            canvast.toDataURL("image/jpeg", 0.2)
          );
          let ret = new File([retblob], this.edSceneName + ".jpg");
          resolve(ret);
        };
      });
    },
    cutMap: function(src) {
      return new Promise((resolve, reject) => {
        let img = new Image();
        img.src = src;
        img.onload = () => {
          let canvast = document.createElement("canvas");
          let ctx = canvast.getContext("2d");
          //(img.width = 4096), (img.height = 2048);
          (canvast.width = 1024), (canvast.height = 2048);
          let ret = [];
          for (let i = 0; i < 4; i++) {
            ctx.drawImage(img, -i * 1024, 0, 4096, 2048);
            let retblob = this.dataURLtoBlob(
              canvast.toDataURL("image/jpeg", 0.95)
            );
            ret.push(new File([retblob], i + this.edSceneName + ".jpg"));
          }
          resolve(ret);
        };
      });
    },
    dataURLtoBlob: function(dataurl) {
      var arr = dataurl.split(","),
        mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]),
        n = bstr.length,
        u8arr = new Uint8Array(n);
      while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
      }
      return new Blob([u8arr], { type: mime });
    },*/
    sceneObjInit: function(objs) {
      console.log(objs);
      this.sceneMap = new Map();
      for (let obj of objs) {
        let sc = new sceneObj();
        sc.unstringify(obj.value, this.getButton.bind(this));
        //sc.jumpObj = [];
        //sc.descObj = [];
        /*if (obj.key == "校门口") {
          this.currentScene = sc;
        }*/
        this.currentScene = "入口刷卡处";
        this.sceneMap.set(obj.key, sc);
      }
      this.loadScene(this.currentScene);
      this.scene.rotation.set(0, 0, 0);
    },
    rendererInit: function() {
      this.scene = new THREE.Scene();
      this.scene.updateMatrixWorld(true);
      this.camera = new THREE.PerspectiveCamera(
        100,
        window.innerWidth / window.innerHeight,
        0.01,
        1000
      );
      this.camera.target = new THREE.Vector3(0, 0, 0);
      this.raycaster = new THREE.Raycaster();
      this.orientH = new orientHandler();
      this.skyBoxGeom = new THREE.SphereGeometry(100, 100, 100);
      this.skyBoxGeom.scale(1, 1, -1);
      this.renderer = new THREE.WebGLRenderer({
        antialias: true,
        precision: "highp"
      });
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      document
        .getElementById("sceneContainer")
        .appendChild(this.renderer.domElement);
      this.editCenter = new THREE.Mesh(
        new THREE.SphereGeometry(0.1, 10, 10),
        new THREE.MeshBasicMaterial({ color: 0xffffff })
      );
      this.editCenter.position.x = 10;
    },
    block() {
      while (!this.skyboxReady);
    },

    initEventListener: function() {
      window.addEventListener("mousedown", this.onMouseDown.bind(this));
      window.addEventListener("mousemove", this.onMouseMove.bind(this));
      window.addEventListener("mouseup", this.onMouseUp.bind(this));
      window.addEventListener("touchstart", this.onTouchStart.bind(this));
      window.addEventListener("touchmove", this.onTouchMove.bind(this), {
        passive: false
      });
      window.addEventListener("touchend", this.onTouchEnd.bind(this));
      window.addEventListener("resize", () => {
        this.camera.aspect = window.innerWidth / window.innerHeight;
        this.camera.updateProjectionMatrix();
        this.renderer.setSize(window.innerWidth, window.innerHeight);
      });
      window.addEventListener(
        "deviceorientation",
        this.onDeviceOrientation.bind(this)
      );
      window.addEventListener(
        "orientationchange",
        this.onOrientationChange.bind(this)
      );
      window.addEventListener("jumpTo", this.jumpTo.bind(this));
      window.addEventListener("textDesc", e => {
        this.textDesc = true;
        this.textDescContent = e.detail.text;
      });
    },

    onMouseDown: function(e) {
      if (this.textDesc || this.selectScene) return;
      //event.preventDefault();
      let ry = new THREE.Vector2(
        (e.clientX / window.innerWidth) * 2 - 1,
        -(e.clientY / window.innerHeight) * 2 + 1
      );
      this.raycaster.setFromCamera(ry, this.camera);
      let intersections = this.raycaster.intersectObjects(this.scene.children);
      for (let obj of intersections) {
        if (!(obj.object.callbk === undefined)) {
          obj.object.callbk();
        }
      }
      if (!this.touchMode) return;
      this.mouseDown = true;
      this.pre.x = e.clientX;
      this.pre.y = e.clientY;
    },

    onMouseMove: function(e) {
      if (this.textDesc || this.selectScene) return;
      if (this.mouseDown) {
        this.eularAngle.x += (this.pre.x - e.clientX) * this.mouseSpeed;
        this.eularAngle.y += (e.clientY - this.pre.y) * this.mouseSpeed;
        this.pre.x = e.clientX;
        this.pre.y = e.clientY;
      }
    },

    onMouseUp: function(e) {
      this.mouseDown = false;
    },

    onTouchStart: function(e) {
      if (this.textDesc || this.selectScene) return;
      //event.preventDefault();
      let ry = new THREE.Vector2(
        (e.touches[0].clientX / window.innerWidth) * 2 - 1,
        -(e.touches[0].clientY / window.innerHeight) * 2 + 1
      );
      this.raycaster.setFromCamera(ry, this.camera);
      let intersections = this.raycaster.intersectObjects(this.scene.children);
      for (let obj of intersections) {
        if (!(obj.object.callbk === undefined)) obj.object.callbk();
      }
      if (!this.touchMode) return;
      this.touchDown = true;
      this.pre.x = e.touches[0].clientX;
      this.pre.y = e.touches[0].clientY;
    },

    onTouchMove: function(e) {
      if (!this.selectScene) event.preventDefault();
      if (this.textDesc || this.selectScene) return;
      if (this.touchDown) {
        this.eularAngle.x +=
          (this.pre.x - e.touches[0].clientX) * this.touchSpeed;
        this.eularAngle.y -=
          (this.pre.y - e.touches[0].clientY) * this.touchSpeed;
        //console.log(this.eularAngle.x,this.eularAngle.y);
        this.pre.x = e.touches[0].clientX;
        this.pre.y = e.touches[0].clientY;
      }
    },

    onTouchEnd: function(e) {
      this.touchDown = false;
    },

    onDeviceOrientation: function(e) {
      this.deviceOrientationData = e;
      if (this.touchMode) return;
      let ret = this.orientH.handleOrient(this.currentScreenOrientation, e);
      this.eularAngle.y = ret.lat;
      this.eularAngle.x = -ret.lon;
    },

    onOrientationChange: function(e) {
      this.currentScreenOrientation = window.orientation;
    },
    jumpTo: function(e) {
      if (!this.sceneMap.has(e.detail.dest)) return;
      this.unloadScene(this.currentScene);
      //this.currentScene = null;
      this.currentScene = e.detail.dest; //this.sceneMap.get(e.detail.dest);
      this.loadScene(this.currentScene);
    },
    getButton: function(typ) {
      let arr = "";
      let geom;
      let mat;
      if (typ == "jump") {
        geom = this.bigGeom; //new THREE.PlaneGeometry(2, 2, 10, 10);
        mat = this.jumpMat; //arr = "https://vr.nekomio.com/file/VR/arr.png";
      } else if (typ == "audio") {
        geom = this.smallGeom; //new THREE.PlaneGeometry(1, 1, 10, 10);
        mat = this.audioMat; //arr = "https://vr.nekomio.com/file/VR/audio.png";
      } else {
        geom = this.smallGeom; //new THREE.PlaneGeometry(1, 1, 10, 10);
        mat = this.textMat; //arr = "https://vr.nekomio.com/file/VR/text.png";
      }
      //let tex = new THREE.TextureLoader().load(arr);
      //tex.magFilter = THREE.NearestFilter;
      /*
      tex.generateMipmaps = false;
      tex.magFilter = THREE.LinearFilter;
      tex.minFilter = THREE.LinearFilter;*/
      let planeObj = new THREE.Mesh(
        geom,
        mat /*
        new THREE.MeshBasicMaterial({
          map: tex,
          alphaTest: 0.5,
          side: THREE.DoubleSide
        })*/
      );
      return planeObj;
    },
    loadScene: function(sc) {
      let cur = this.sceneMap.get(sc);
      this.loadBgImg(cur.bgSrc);

      for (let obj of cur.jumpObj) {
        this.scene.add(obj);
      }

      for (let obj of cur.descObj) {
        this.scene.add(obj);
      }
      for (let obj of cur.normalObj) {
        this.scene.add(obj);
      }
    },
    unloadScene: function(sc) {
      let cur = this.sceneMap.get(sc);
      this.unloadBgImg();
      for (let obj of cur.jumpObj) {
        this.scene.remove(obj);
      }
      for (let obj of cur.descObj) {
        this.scene.remove(obj);
      }
      for (let obj of cur.normalObj) {
        this.scene.remove(obj);
      }
    },
    loadBgImg: function(src) {
      this.skyboxReady = false;
      this.skyBoxTexture = new THREE.TextureLoader().load(src, () => {
        this.skyboxReady = true;
        this.skyBoxTexture.generateMipmaps = false;
        this.skyBoxTexture.magFilter = THREE.LinearFilter;
        this.skyBoxTexture.minFilter =
          THREE.LinearFilter; /*
        if (!(JSON.stringify(this.skyBoxMesh) === "{}")) {
          this.scene.remove(this.skyBoxMesh);
          this.skyBoxMesh = null;
        }*/
        /*this.skyBoxMat.map  = new THREE.MeshBasicMaterial({
          map: this.skyBoxTexture
        });*/

        if (this.skyBoxMesh == 0) {
          this.skyBoxMat = new THREE.MeshBasicMaterial({
            map: this.skyBoxTexture
          });
          this.skyBoxMesh = new THREE.Mesh(this.skyBoxGeom, this.skyBoxMat);
          this.scene.add(this.skyBoxMesh);
        } else {
          this.skyBoxMat.map = this.skyBoxTexture;
        }

        //this.skyBoxMesh = new THREE.Mesh(this.skyBoxGeom, this.skyBoxMat);
        //this.scene.add(this.skyBoxMesh);
      });
    },
    unloadBgImg: function() {
      this.skyBoxMat.map.dispose();
      this.skyBoxMat.map = null;

      /*this.skyBoxMat.dispose();*/
      this.skyBoxTexture.dispose();
      this.skyBoxTexture = null;
      //this.skyBoxMat = null;
      this.skyBoxPartialOK = [false, false, false, false];
    },
    partialLoadBgImg: function(id) {
      this.skyBoxPartialOK[id] = true;
      let nowname = this.currentScene;
      let src = this.sceneMap.get(this.currentScene).partialSrc[id];
      let tm = window.setTimeout(() => {
        let tex = new THREE.TextureLoader().load(
          src,
          /*this.sceneMap.get(this.currentScene).partialSrc[id],*/
          () => {
            if (this.currentScene != nowname) {
              tex.dispose();
              tex = null;
              return;
            }
            tex.generateMipmaps = false;
            tex.magFilter = THREE.LinearFilter;
            tex.minFilter = THREE.LinearFilter;
            this.renderer.copyTextureToTexture(
              { x: id * 1024, y: 0 },
              tex,
              this.skyBoxTexture
            );
            tex.dispose();
            tex = null;
          }
        );
        window.clearTimeout(tm);
      }, 100);
    },
    Update() {
      if (this.eularAngle.y > 85) {
        this.eularAngle.y = 85;
      } else if (this.eularAngle.y < -85) {
        this.eularAngle.y = -85;
      }
      if (this.eularAngle.x >= 360) {
        this.eularAngle.x %= 360;
      } else if (this.eularAngle.x < 0) {
        this.eularAngle.x = (this.eularAngle.x % 360) + 360;
      }
      let eularRadX = THREE.Math.degToRad(this.eularAngle.x);
      let eularRadY = THREE.Math.degToRad(90 - this.eularAngle.y);
      this.camera.target.x = 500 * Math.sin(eularRadY) * Math.cos(eularRadX);
      this.camera.target.y = 500 * Math.cos(eularRadY);
      this.camera.target.z = 500 * Math.sin(eularRadX) * Math.sin(eularRadY);
      this.camera.lookAt(this.camera.target);
      this.renderer.render(this.scene, this.camera);
      this.editCenter.position.x =
        10 * Math.sin(eularRadY) * Math.cos(eularRadX);
      this.editCenter.position.y = 10 * Math.cos(eularRadY);
      this.editCenter.position.z =
        10 * Math.sin(eularRadX) * Math.sin(eularRadY);

      if (!this.skyboxReady) return;
      if (270 <= this.eularAngle.x || this.eularAngle.x < 90) {
        if (!this.skyBoxPartialOK[1]) {
          this.partialLoadBgImg(1);
        }
        if (!this.skyBoxPartialOK[2]) {
          this.partialLoadBgImg(2);
        }
      } else {
        if (!this.skyBoxPartialOK[3]) {
          this.partialLoadBgImg(3);
        }
        if (!this.skyBoxPartialOK[0]) {
          this.partialLoadBgImg(0);
        }
      }
    },

    animate: function() {
      requestAnimationFrame(this.animate);
      this.Update();
    }
  },
  mounted() {
    this.jumpTex = new THREE.TextureLoader().load(
      "https://vr.nekomio.com/file/VR/arr.png"
    );
    this.textTex = new THREE.TextureLoader().load(
      "https://vr.nekomio.com/file/VR/text.png"
    );
    this.audioTex = new THREE.TextureLoader().load(
      "https://vr.nekomio.com/file/VR/audio.png"
    );
    this.jumpMat = new THREE.MeshBasicMaterial({
      map: this.jumpTex,
      alphaTest: 0.5,
      side: THREE.DoubleSide
    });
    this.audioMat = new THREE.MeshBasicMaterial({
      map: this.audioTex,
      alphaTest: 0.5,
      side: THREE.DoubleSide
    });
    this.textMat = new THREE.MeshBasicMaterial({
      map: this.textTex,
      alphaTest: 0.5,
      side: THREE.DoubleSide
    });
    let bk = "https://vr.nekomio.com/admin/api/login";
    this.$axios
      .post(bk, null, {
        headers: { "Content-Type": "text/plain" }
      })
      .then(res => {
        this.token = res.data;
      });
    this.$axios
      .get("https://vr.nekomio.com/file/VR/vrconfig.txt")
      .then(res => {
        this.mode = res.data.mode;
        this.sceneObjInit(res.data.scenes);
        this.animate();
      });
    this.rendererInit();
    this.initEventListener();
    //this.animate();
  }
};
</script>
<style>
#textdesc {
  position: absolute;
  top: 10%;
  margin-left: -125px;
  left: 50%;
  height: 400px;
  width: 210px;
  border: none;
  border-radius: 0px;
  background-color: #ffffff;
  padding-left: 20px;
  padding-right: 20px;
  text-align: left;
  color: #303030;
  font-size: 18px;
  line-height: 30px;
  box-shadow: 3px 4px 10px #3a3a3a;
  z-index: 5;
}
#textdescbtn {
  position: absolute;
  top: 360px;
  margin-left: -125px;
  left: 50%;
  height: 40px;
  width: 250px;
  border: none;
  background-color: #ffffff;
  box-shadow: 0px -2px 2px #d1d1d1;
  font-weight: bold;
  font-size: 20px;
  line-height: 40px;
  text-align: center;
  vertical-align: middle;
  color: #303030;
}
body {
  margin: 0;
}
.select {
  position: absolute;
  top: 5%;
  left: 60%;
  height: 23px;
  width: 90px;
  border: none;
  background-color: #ffffff;
  color: #2d107f;
  z-index: 4;
}
#selectPanel {
  position: absolute;
  top: 10%;
  margin-left: -125px;
  left: 50%;
  height: 400px;
  width: 250px;
  border: none;
  border-radius: 0px;
  background-color: #ffffff;
  text-align: left;
  color: #303030;
  font-size: 18px;
  line-height: 30px;
  box-shadow: 3px 4px 10px #3a3a3a;
  z-index: 10;
  overflow-x: hidden;
  overflow-y: scroll;
  -webkit-overflow-scrolling: touch;
}
#selectPanel::-webkit-scrollbar {
  display: none;
}
.selectTag {
  margin-left: 5px;
  left: 50%;
  margin-top: 4px;
  height: 50px;
  width: 200px;
  background-color: #ffffff;
  padding-left: 20px;
  padding-right: 20px;
  text-align: center;
  color: #303030;
  font-size: 18px;
  line-height: 50px;
  border-bottom: 1px solid #d3d3d3;
  z-index: 10;
}
#modebutton {
  position: absolute;
  top: 90%;
  left: 60%;
  height: 30px;
  border: none;
  border-radius: 5px;
  background-color: #616161;
  opacity: 0.7;
  color: #ffffff;
}
#modebutton2 {
  position: absolute;
  top: 90%;
  left: 60%;
  height: 30px;
  border: none;
  border-radius: 5px;
  background-color: #616161;
  opacity: 0.7;
  color: #ffffff;
}
.edit {
  position: absolute;
  top: 10%;
  margin-left: -150px;
  left: 50%;
  height: 30px;
  width: 300px;
  border: none;
  border-radius: 10px;
  background-color: #616161;
  opacity: 0.7;
  color: #000000;
}
#editbar {
  position: absolute;
  top: 5%;
  left: 50%;
  margin-left: -150px;
  width: 300px;
  background-color: #616161;
  opacity: 0.7;
  color: #000000;
}
.editorPanel {
  position: absolute;
  top: 25%;
  margin-left: -150px;
  left: 50%;
  height: 160px;
  width: 300px;
  border: none;
  border-radius: 10px;
  background-color: #616161;
  opacity: 0.7;
  color: #000000;
}
.tab {
  width: 300px;
}
.tab-item {
  line-height: 29px;
  height: 30px;
  margin-top: -1px;
  width: 300px;
  border: 1px solid #ddd;
  text-align: center;
  cursor: pointer;
}
.tab-item:hover input[type='radio']:checked + .tab-item {
  background: #ddd;
}
.tab-content-item {
  transition: all 0.4s;
  display: none;
  width: 300px;
  height: 200px;
  text-align: center;
  border: 1px solid #ddd;
  background: #ddd;
}
.tab-content-item ul {
  padding: 6px 5px;
  display: flex;
  flex-wrap: wrap;
  list-style-type: none;
}
.tab-content-item ul .byr-floor-room {
  padding: 6px 5px;
  color: #2d107f;
  flex: 1;
  font-size: 16px;
  cursor: pointer;
}
.tab-content-item ul .byr-floor-room:hover {
  color: red;
}
input[type='radio'] {
  display: none;
}
input[type='radio']:checked + .tab-content-item {
  display: block;
}
.byr-mask-show {
  position: absolute;
  z-index: 2;
  width: 40px;
  font-size: 24px;
  height: 40px;
  line-height: 40px;
  right: 10%;
  cursor: pointer;
  transition: all 0.4s;
  color: #bbb;
}
.byr-mask-show:hover {
  color: #ffb800;
}
.web {
  width: 375px;
  height: 666px;
  background-color: #eeeeee;
  position: absolute;
  z-index: 1;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  opacity: 1;
  transition: all 0.5s;
}
.web:hover {
  background-color: #f2f2f2;
  opacity: 1;
}
.web:hover .byr-main-title {
  color: #ffb800;
  font-size: 20px;
}
.sound_controller {
  cursor: pointer;
  position: absolute;
  right: 30px;
  top: 30px;
  width: 30px;
  height: 30px;
}
.sound_controller img {
  width: 30px;
  height: 30px;
}
.byr-main-title {
  transition: all 0.2s;
  text-align: center;
  color: #ffb800;
  font: normal 700 20px/1 'Microsoft YaHei UI';
}
.byr-sub-title::before {
  color: #2d107f;
  content: '地点名下';
  font-size: 20px;
  margin-right: 5px;
}
.byr-sub-title {
  text-align: center;
  color: #2d107f;
  font-size: 16px;
}
.accordion {
  width: 300px;
  margin: 30px auto;
}
.floor_up,
.floor_down,
.love,
.message {
  cursor: pointer;
}
.floor_up img,
.floor_down img {
  position: absolute;
  right: 30px;
  width: 45px;
  height: 45px;
}
.floor_up img {
  top: 450px;
}
.floor_down img {
  top: 510px;
}
.message img {
  position: absolute;
  width: 25px;
  height: 25px;
  left: 40px;
  bottom: 30px;
}
.love img {
  position: absolute;
  width: 25px;
  height: 25px;
  right: 40px;
  bottom: 30px;
}
.count {
  position: absolute;
  bottom: 32px;
  right: 65px;
  padding: 0 10px;
}

</style>
<!-- Add "scoped" attribute to limit CSS to this component only -->