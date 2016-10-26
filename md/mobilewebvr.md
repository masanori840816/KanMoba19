<div style='font-size: 80px; position:absolute; top:30%; left: 10%;'>
Mobile + Web + VR
</div>
<div style='font-size:40px; position: absolute; bottom: 5%; left: 5%;'>
2016.10.26 @関モバ
</div>
<img id='top_image' src='img/pumpkin.png' style='width: 256px; height: 256px; position:absolute; bottom:2%; right:4%;'></img>
---
# Who?
### Name: Masui Masanori

### Twitter: [@masanori_msl](https://twitter.com/masanori_msl)

### Blog: [vaguely](http://mslgt.hatenablog.com/)

### GitHub: https://github.com/masanori840816

### App: [SearchWakayamaToilet](https://play.google.com/store/apps/details?id=jp.searchwakayamatoilet)
---
<div style='font-size: 70px; position:absolute; top: 40%;'>
もうすぐHelloweenですね
</div>
---
<div style='font-size: 40px; position:absolute; top: 20%;'>
Helloweenといえば
</div>
<div style='font-size: 70px; position:absolute; top: 40%; left: 40%;'>
仮装
</div>
---
<div style='font-size: 40px; position:absolute; top: 20%;'>
仮装といえば
</div>
<div style='font-size: 70px; position:absolute; top: 40%; left: 40%;'>
仮想
</div>
---
<div style='font-size: 70px; position:absolute; top: 40%; left: 25%;'>
仮想現実(VR)
</div>
---
# VR
<ul style='font-size: 40px; absolute; top: 30%;'>
<li>Daydream</li>
<li>Gear VR</li>
<li>Cardboard</li>
<li>ハコスコ etc.</li>
</ul>
---
<div style='font-size: 70px; position:absolute; top: 40%; left: 30%;'>
WebVR
</div>
---
# WebVR
<div style='font-size: 40px; position:absolute; top: 45%;'>
three.jsというJavascriptのライブラリを使って、Web Browser上で表示する
</div>
---
# Sample
<div style='font-size: 40px; position:absolute; top: 45%;'>
<a href='https://github.com/masanori840816/MobileWebVrDemo'>MobileWebVrDemo - GitHub</a>
</div>
---
# 準備1
## [Server side]Spring Boot
### 今回はローカルサーバー上で動作すれば問題ないので、[Spring Initializer](http://start.spring.io/)などで適当に作成
---
# 準備2
## HTML

```xml
<!DOCTYPE html>
<html lang="jp">
    <head>
        <meta charset="UTF-8" />
        <title>MainPage</title>
        <meta name="viewport" content="width=device-width,
            user-scalable=no,minimum-scale=1.0,
            maximum-scale=1.0, shrink-to-fit=no"/>
        <meta name="apple-mobile-web-app-capable" content="yes" />
        <meta name="apple-mobile-web-app-status-bar-style"
            content="black-translucent" />
        <link rel='stylesheet' type="text/css"
            href="css/mainpage.css" />
    </head>
    <body>
```
---
# 準備2-2
## HTML

```xml
        <script src='js/three.min.js'></script>
        <script src='js/VRControls.js'></script>
        <script src="js/VREffect.js"></script>
        <script src="js/webvr-polyfill.js"></script>
        <script src="js/webvr-manager.js"></script>
        <script src="js/MTLLoader.js"></script>
        <script src="js/OBJLoader.js"></script>
        <script src="js/stats.min.js"></script>
        <script src="js/mainpage.js"></script>
    </body>
</html>
```
---
# 準備3
## CSS
### 画面サイズの指定など

```xml
body {
    width: 100%;
    height: 100%;
    background-color: #000;
    color: #fff;
    margin: 0px;
    padding: 0;
    overflow: hidden;
}
```
---
# 準備4
## 3Dモデルの準備
<img src='img/blender.jpg' style='width:80%;'></img>
---
# Javascript Libraries
### [three.js](https://threejs.org/)<br> - 3Dモデルの表示など


### [webvr-boilerplate](https://github.com/borismus/webvr-boilerplate)<br> - Mobile用のWebVR表示マネージャーを提供


### [webvr-polyfill](https://github.com/googlevr/webvr-polyfill)<br> - webvr-boilerplateの依存ライブラリ


### [stats.js](https://github.com/mrdoob/stats.js)<br> - 実行時のFPSを計測
---
# 実装
<ul style='font-size: 40px; absolute; top: 30%;'>
<li>3D空間を作る</li>
<li>カメラを追加する</li>
<li>ライトを追加する</li>
<li>3Dモデルを追加する</li>
<li>繰り返し描画する</li>
<li>実行速度の計測</li>
</ul>
---
# 3D空間を作る
```xml
renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setPixelRatio(window.devicePixelRatio);

document.body.appendChild(renderer.domElement);

// Sceneの生成
scene = new THREE.Scene();

// VR用コントローラを生成
controls = new THREE.VRControls(camera);

// VR用エフェクトを生成（2分割の画面を構築する）
effect = new THREE.VREffect(renderer);
effect.setSize(window.innerWidth, window.innerHeight);

// VRマネージャの生成
manager = new WebVRManager(renderer, effect);
```
---
# カメラを追加する
```xml
// カメラを生成
camera = new THREE.PerspectiveCamera(75,
    window.innerWidth / window.innerHeight, 0.1, 10000);
camera.position.set(0, 0, 0);
```
---
# ライトを追加する
```xml
var light = new THREE.DirectionalLight(0xffffff, 0.1);
light.position.set(-1, 1, 1);
light.castShadow = true;
scene.add(light);

var ambient = new THREE.AmbientLight(0xffffff, 0.5);
ambient.castShadow = true;
ambient.position.set(0,0,-5);
scene.add(ambient);

var pointlight = new THREE.PointLight(0xffffff, 0.8);
pointlight.position.set(0.3, 0.4, -3);
pointlight.scale.set(1, 1, 1);
pointlight.castShadow = true;
scene.add(pointlight);
```
---
# 3Dモデルを追加する
```xml
// Materialの読み込み
var mtlLoader = new THREE.MTLLoader();
mtlLoader.load("models/pumpkin.mtl",
    function(targetMaterial) {
        targetMaterial.preload();
        // Materialの読み込みが完了したらObjectを読み込む.
        var objLoader = new THREE.OBJLoader();
        objLoader.setMaterials(targetMaterial);
        objLoader.load("models/pumpkin.obj",
            function (targetObject) {
                objectModel = targetObject.clone();
                objectModel.scale.set(1, 1, 1);
                objectModel.rotation.set(0, 3, 0);
                objectModel.position.set(0, 0, 0);

                obj = new THREE.Object3D();
                obj.add(objectModel);
                // sceneに追加
                scene.add(obj);
        });
    });
```
---
# 繰り返し描画する
```xml
this.animate = function(timestamp) {
    // VRコントローラを更新
    controls.update();
    // VRマネージャからシーンを描画
    manager.render(scene, camera, timestamp);

    // ループして実行
    requestAnimationFrame(animate);
};
initialize();
// アニメーションの開始
animate(performance ? performance.now() : Date.now());
```
---
# 実行速度の計測
### VRの実装自体には関係ないが、<bt>快適な体験には60FPS以上が必須
```xml
this.initialize = function(){
    // FPSの計測.
    stats = new Stats();
    stats.showPanel(0); // 0: fps, 1: ms, 2: mb, 3+: custom
    document.body.appendChild( stats.dom );
～ 省略 ～
this.animate = function(timestamp) {
    // FPS計測開始
    stats.begin();
～ 省略(描画の更新処理) ～
    // FPS計測終了
    stats.end();
    // アニメーションループ
    requestAnimationFrame(animate);
```
---
# 結果
## PC
<img src='img/result.jpg' style='width: 80%; height:80%;'></img>
---
# 結果2
## mobile
<img src='img/result_mobile.jpg' style='width: 80%; height:80%;'></img>
---
# 対応端末
### 手持ちの端末で表示できるか確認した結果
<table style='absolute; top: 30%; font-size: 25px;' border='1'>
<tr>
<th>Device</th>
<th>Browser</th>
<th>Result</th>
</tr>
<tr>
<th>Nexus 5X</th>
<th>Chrome, Firefox</th>
<th>問題なし</th>
</tr>
<tr>
<th>iPhone 7</th>
<th>Safari, Firefox</th>
<th>問題なし</th>
</tr>
<tr>
<th>iPhone 5s</th>
<th>Safari, Firefox</th>
<th>表示は問題ないが、<br>ヘッダとフッタが消えない?</th>
</tr>
<tr>
<th>Xperia Z</th>
<th>Chrome</th>
<th>問題なし</th>
</tr>
<tr>
<th>Nexus 7(2013)</th>
<th>Chrome</th>
<th>表示は問題ないが、<br>モバイルとして認識されない</th>
</tr>
</table>
---
# 課題
<ul style='font-size: 40px; absolute; top: 30%;'>
<li>LandscapeModeにする前に<br>VRモードに切り替えると、<br>
その後デバイスを回転させたときに<br>カメラが正しく回転しない</li>
<li>描画速度が規定値に満たない<br>(現時点でも60FPS以下になってしまう)</li>
</ul>
---
# 終わりに
<ul style='font-size: 40px; absolute; top: 30%;'>
<li>3D表示したりVRしたりJavascriptすごい<br>(小並感)</li>
<li>ハードルは高いが、<br>インストールせずに使えるメリットは大きい</li>
<li>音声の再生部分も挑戦してみたい</li>
</ul>
---
# 参考
<ul style='font-size: 30px; absolute; top: 30%;'>
<li><a href="https://html5experts.jp/edo_m18/18552/">JSだけでVRできる！『WebVR』ことはじめ - HTML5Experts.jp</a></li>
<li><a href="https://github.com/borismus/webvr-boilerplate">webvr-boilerplate - borismus - GitHub</a></li>
<li><a href="http://www.oreilly.co.jp/books/9784873117706/">初めてのThree.js 第2版 - O'Reilly Japan</a></li>
<li><a href="http://qiita.com/mo4_9/items/a934a71a6078834ecbed">3Dモデルの表示 - Qiita</a></li>
<li><a href="http://qiita.com/inomasa/items/6bc4f91bd10635266327">three.js を使った3Dモデル表示 part1 - Qiita</a></li>
<li><a href="http://qiita.com/yk-nakamura/items/2ea2e9f5399a9d930a6b">three.jsにおけるシェーディング<br>：光源と反射材質の概要、法線ベクトルの設定方法<br> - Qiita</a></li>
</ul>
---
<div style='font-size: 70px; position:absolute; top: 40%;'>
Thank You !
</div>
<img id='top_image' src='img/pumpkin.png' style='width: 256px; height: 256px; position:absolute; bottom:2%; right:4%;'></img>
