# Three.js documents
melodyWaver with three.js & melodyne

## Contents
0. [먼저 알아야 할 것들](https://threejsfundamentals.org/threejs/lessons/kr/threejs-prerequisites.html)
1. [three.js란?](https://threejsfundamentals.org/threejs/lessons/kr/threejs-fundamentals.html)
2. [반응형 디자인](https://threejsfundamentals.org/threejs/lessons/kr/threejs-responsive.html)
3. [three.js의 원시 모델](https://threejsfundamentals.org/threejs/lessons/kr/threejs-primitives.html)
4. [three.js의 씬 그래프](https://threejsfundamentals.org/threejs/lessons/kr/threejs-scenegraph.html)


## reference
1. [threejsfundamentals](https://threejsfundamentals.org/threejs/lessons/kr/threejs-fundamentals.html)
  
------------

# 1. three.js란?
   
1-0. three.js 구조

* Renderer : Scene과 Camera 객체를 넘겨 받아 카메라의 frustum 안 3D scene의 일부를 2D 이미지로 렌더링한다.
* Scene graph : Scnen + Camera + Mesh(Geometry + Material) + Texture + Light

> 공간 - Scene
> 
> 피사체: 부피, 질감 - Mesh = Geometry + Material
> 
> 카메라 - Camera
> 
> 빛 - Light

------------

1-1. Geometry와 Material

 반지름이 같은 쇠 구슬과 유리 구슬이 있다. Scene에 두 구슬을 그리고자 할 때, 쇠 구슬과 유리 구슬을 각각 아무것도 없는 상태로부터

 표면의 점을 하나씩 찍어가며 그려낼 수 있을 것이다. 하지만, 구슬의 '구형 기하학적 형태'를 별도로 뺄 수 있다면 어떨까?

 같은 기하학적 형태를 갖고, 표면만 다른 쇠 구슬과 유리 구슬처럼 여러 물체를 그릴 때 매번 기하학적 형태를 정의할 필요가 없어질 것이다.

 또한, 이렇게 뼈대와 표면이 분리되면 기존에 정의한 물체의 표면을 업데이트 하기도 쉬워진다.

Geometry
> 기하학적 형태, 뼈대를 담당하는 부분 (반지름이 r인 구형 물체)라는 정보

Material
> 특정한 질감, 색, 반사율 등을 갖는 물체의 표면 (은색이고 매끈하며 반사율이 높은 쇠 표면, 투명하며 빛을 대부분 투과시키는 유리 표면) 따위의 정보

Mesh(물체) = Geometry + Material
> 정확한 학문적인 의미를 갖고 있다기보다는, 어느정도 관용적으로 사용되는 용어인 듯 하다.

------------

1-2. Camera

``` Javascript
  const fov = 75;
  const aspect = 2;
  const near = 0.1;
  const far = 5;
  const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
```
fov
> field of view(시야각), radian이 아닌 degree를 단위로 사용한다

aspect
> canvas의 가로/세로 비율

near, far
> 카메라 앞에 렌더링되는 공간 범위

기본적으로 카메라는 -Z 축 +Y 축을 바라본다.

---

1-3. requestAnimationFrame

브라우저에 애니메이션 프레임을 요청하는 함수이다. 인자로 실행할 함수를 전달한다.

1-4. Light

``` Javascript
  const color = 0xFFFFFF;
  const intensity = 1;
  const light = new THREE.DirectionalLight(color, intensity);
  light.position.set(-1, 2, 4);
  scene.add(light);
```

position.set
> DirectionalLight에는 위치(position)와 목표(target) 속성이 default(0, 0, 0)로 있다.

# 2. 반응형 디자인

2-0. 반응형이란?

 웹에서 반응형이란, 웹 페이지를 PC, 타블렛, 스마트폰 등 다양한 환경에서 이용하기 용이하도록 사이즈에 맞춰 콘텐츠를 최적화하는 것을 의미한다.

``` javascript
  <canvas id="c"></canvas>
```
  canvas 요소는 기본적으로 300x150 pixel을 갖는다.

``` javascript
  <style>
  html, body {
    margin: 0;
    height: 100%;
  }
  #c {
    width: 100%;
    height: 100%;
    display: block; // canvas 요소의 default display 속성은 inline이다. (글자처럼 취급) => display: block 으로 변경
  }
  </style>
```
  body 요소는 default로 5 pixel의 margin이 지정되어 있으니, margin: 0
  html과 body 요소의 height를 지정하지 않으면, contents의 높이만큼만 커지니, height: 100%로 창 전체를 채운다.

---

2-1. 해상도 errorFix

창 크기에 따라, cube가 직육면체로 변한다.

``` javascript
  const canvas = renderer.domElement;
  camera.aspect = canvas.clientWidth / canvas.clientHeight;
  camera.updateProjectionMatrix();
```

 camera의 aspect(비율) 속성을 canvas의 화면 크기에 맞게 맞춘다.

 canvas의 원본 크기, 해상도는 drawingbuffer라고 불린다.

 Three.js에서는 'renderer.setSize' method를 호출하여 canvas의 드로잉버퍼 크기를 지정할 수 있다.

 이 때, 크기는 "canvas의 디스플레이 크기"를 고른다.

``` javascript
  function resizeRendererToDisplaySize(renderer) {
    const canvas = renderer.domElement;
    const width = canvas.clientWidth;
    const height = canvas.clientHeight;
    const needResize = canvas.width !== width || canvas.height !== height; // canvas 스펙상, resizing은 화면을 다시 렌더링해야 하므로, 같은 사이즈 일 때는 리사이징을 하지 않음으로써 불필요한 자원 낭비를 막을 필요가 있다.
    if (needResize) {
      renderer.setSize(width, height, false);
    }
    return needResize;
  }
```

 canvas의 크기가 다르다면, 'renderer.setSize' method를 호출해 새로운 width와 height를 넘겨준다.

 'renderer.setSize' method는 기본적으로 CSS의 크기를 설정하므로, 마지막 인자로 false를 넘겨준다.

 (canvas가 다른 요소와 어울리려면, three.js에서 CSS를 제어하는 것 보다, 다른 요소와 같이 CSS로 제어하는 것이 일관성 있는 프로그래밍)

 따라서, 해상도 문제와 resizing을 해결할 수 있는 총 코드는 다음과 같이 된다.

 ``` javascript
  if (resizeRendererToDisplaySize(renderer)) {
    const canvas = renderer.domElement;
    camera.aspect = canvas.clientWidth / canvas.clientHeight;
    camera.updateProjectionMatrix();
  }
 ```

2-2. HD-DPI 디스플레이 다루기

 Three.js로 HD-DPI를 다루는 3가지 방법

 아무것도 하지 않기
 > 3D 렌더링은 많은 GPU 자원을 소모한다. 2018년 기준, HD-DPI는 약 3배의 해상도를 지녔다. 즉, HD-DPI가 아닌 기기와 비교했을 때, 한 픽셀 당 픽셀 수가 1:9
 > 즉, 9배 많은 픽셀을 처리해야 한다.

 renderer.setPixelRatio method를 이용해 해상도 배율을 알려주기
 > 브라우저로부터 CSS pixel과 실제 기기 pixel의 배율을 맏아 three.js에게 넘겨준다.

 ```javascript
  renderer.setPixelRatio(window.devicePixelRatio);
 ```

> 성능 이슈가 있는 것으로 보인다 (추천X)

canvas를 리사이징 할 때 직접 계산하기

``` javascript
  function resizeRendererToDisplaySize(renderer) {
    const canvas = renderer.domElement;
    const pixelRatio = window.devicePixelRatio;
    const width = canvas.clientWidth * pixelRatio | 0;
    const height = canvas.clientHeight * pixelRtio | 0;
    const needResize = canvas.width !== width || canvas.height !== height;
    if (needResize) {
      renderer.setSize(width, height, false);
    }
    return needResize;
  }
```

# 3. three.js의 원시 모델

3-0. 원시 모델

 대부분의 원시 모델은 Geometry와 BufferGeometry가 짝을 이룬다.

 BufferGeometry => 원시 모델을 사용하지 않을 계획이거나, 기하학 모델을 수학적으로 계산하는 데 익숙한 경우
 > BufferGeometry 기반의 원시 모델은 성능에 최적화된 모델이다. geometry의 정점들은 바로 렌더링 시 GPU에서 불러오기 좋은 배열 형태로 최적화 된다.

 Geometry
 > 3D 정점을 만드는 데는 'Vector3' Class, 삼각형을 만드는 데는 'Face3' Class 등 Javascript 기반 calss로 이루어져 있다.

 [Custom geometry를 만드는 법은 다음을 참조](https://threejsfundamentals.org/threejs/lessons/kr/threejs-custom-geometry.html)

 x, y 좌표와 Object3D 를 매개변수로 받아 scene에 추가하는 addObject 함수

 ```javascript
  const objects = [];
  const spread = 15;

  function addObject(x, y, obj) {
    obj.position.x = x * spread;
    obj.position.y = y * spread;

    scene.add(obj);
    objects.push(obj);
  }
 ```

 물체를 무작위로 채색하는 함수 (hue, 채도, 명도)로 색을 지정하는 Color의 기능을 활용한다.

 ```javascript
  function createMaterial() {
    const material = new THREE.MeshPhongMaterial({
      side: THREE.DoubleSide, // three.js에게 삼각형의 양면 모두를 렌더링하라고 알려준다.
    });

    const hue = Math.random();
    const saturation = 1;
    const luminance = .5;
    material.color.setHSL(hue, saturation, luminance);

    return material;
  }
 ```

 hue
 > 0~1까지의 색상값을 고리 모양으로 배치한 것으로, 12시 = 빨강, 4시 = 녹색, 8시 = 파랑

 saturation(채도)
 > 0~1까지이며, 0 = 색이 가장 옅은 것, 1 = 색이 가장 진한 것을 의미한다.

 luminance(명도)
 > 0.0 = 검정, 1.0 = 하양, 0.5 = 색이 가장 풍부 / 명도가 0.0 -> 0.5로 갈수록 검정에서 hue에 가까워지고, 0.5 -> 1.0으로 갈수록 hue에서 하양에 가까워진다.

 side: THREE.DoubleSide -> 렌더링 속도에 영향을 주므로, 필요한 물체에만 지정하는게 좋다.
 > 구, 정육면체 같은 물체는 보이지 않는 안쪽 면을 렌더링 할 필요가 없지만, 예제의 경우 PlaneBufferGeometry나 ShapeBufferGeometry 등 안쪽 면이 없는 물체가 존재하기 때문에 'side: THREE.DoubleSide' 옵션을 설정하지 않으면, 반대편에서 봤을 때 물체가 사라진 것처럼 보인다.

매개변수로 받은 geometry와 앞서 만든 createMaterial 함수를 사용해 무삭위로 색칠한 물체를 만들고, addObejct 함수로 scene에 추가하는 함수

```javascript
  function addSolidGeometry(x, y, geometry) {
    const mesh = new THREE.Mesh(geometry, createMaterial());
    addObject(x, y, mesh);
  }
```

이를 통해, 정육면체를 만든다고 하면 다음과 같다.

```javascript
  {
    const width = 8;
    const height = 8;
    const depth = 8;
    addSolidGeometry(-2, -2, new THREE.BoxBufferGeometry(width, height, depth));
  }
```
---

3-1. 헬퍼 객체

EdgesGeometry

``` javascript
  const size = 8;
  const widthSegments = 2;
  const heightSegments = 2;
  const depthSegments = 2;
  const boxGeometry = new THREE.BoxBufferGeometry(
    size, size, size,
    widthSegments, heightSegments, depthSegments);
  const geometry = new THREE.EdgesGeometry(boxGeometry); // EdgesGeometry로 변환한다
```

 다른 geometry를 받는 헬퍼 객체이다. 각-면 사이의 angle이 일정 값 이상일 때만 모서리를 표시한다.

WireframeGeometry

``` javascript
  const size = 8;
  const widthSegments =  1;  
  const heightSegments = 2;  
  const depthSegments = 2;  
  const geometry = new THREE.WireframeGeometry(
    new THREE.BoxBufferGeometry(
      size, size, size,
      widthSegments, heightSegments, depthSegments));
```

 매개변수로 받은 geometry의 모서리 하나 당 하나의 선분(2개의 점)을 가진 geometry를 생성한다.
 
 ...

---

3.2 textBufferGeometry

 textBufferGeometry는 text의 mesh를 생성하기 위해 3D font data를 필요로 한다. (비동기 load)

 ``` javascript
  {
    const loader = new THREE.FontLoader();
    // promisify font loading
    function loadFont(url) {
      return new Promise((resolve, reject) => {
        loader.load(url, resolve, undefined, reject);
      })
    }

    async function doit() {
      const font = await loadFont('resources/threejs/fonts/helvetiker_regular.typeface.json');
      const geometry = new THREE.TextBufferGeometry('three.js', {
        font: font,
        size: 3.0,
        height: .2,
        curveSegments: 12,
        bevelEnabled: true,
        bevelThickness: 0.15,
        bevelSize: .3,
        bevelSegments: 5,
      });
      const mesh = new THREE.Mesh(geometry, createMaterial());
      /* Three.js의 텍스트의 default 회전축은 왼쪽 모서리로, 중앙을 중심으로 돌게 한다 */
      geometry.computeBoundingBox(); // 경계 좌표 계산
      geometry.boundingBox.getCenter(mesh.position).multiplyScalar(-1);
      /* 중앙을 중심으로 돌게 하려면 Three.js에게 geometry의 bounding box(경계 좌표)를 계산해 달라고 요청한 뒤, bounding box의 getCenter 메서드에 해당 mesh의 위치값 객체를 넘겨주어야 한다. 이러면 getCenter 메서드는 넘겨받은 위치값의 중앙 좌표값을 자신의 위치값으로 복사한다. 그리고 변경된 위치값 객체를 반환하는데, 이 객체의 multiplyScalar(-1) 메서드로 전체 텍스트의 회전 중심이 텍스트의 중앙에 오도록 조정할 수 있다. */

      const parent = new THREE.Object3D();
      parent.add(mesh);

      addObject(-1, -1, parent);
    }
    doit();
  }
  ```

3.3 PointsMaterial & sizeAttenuation

[three.js의 원시 모델](https://threejsfundamentals.org/threejs/lessons/kr/threejs-primitives.html) 맨 하단의 예제 참조.
 
# 4. three.js의 씬 그래프

4-0. Scene Graph란?

 3D 엔진에서 씬 그래프란, 요소(node)의 계층 구조를 그림으로 나타낸 것으로, 여기서 각 요소는 각각의 "지역 공간(local space)"을 가리킨다.

 다음과 같은 태양계 예제가 있다고 가정하자.

![4-1](./threeJsEx/images/4-1.jpg)

 이와 같이, 우리는 지구에 살지만 지구의 자전/자전축 공전은 크게 신경쓰지 않는다. 이것은 모두 지구의 일이기 때문이다.
 우리가 걷거나, 뭔가를 타고 이동하는 동작은 지구의 일과는 무관한 것처럼 보인다(우리는 그저, '지구'라는 지역공간에서 제 몫을 다하면 된다.)

 그렇다면, 태양의 주변을 지구가 돌고 - 지구의 주변을 달이 도는 태양계 model을 만들어보자.

---

4-1. Sun

 ```javaScript
  {
    // 회전값을 업데이트할 객체들
    const objects = [];

    // 하나의 geometry로 모든 태양, 지구, 달을 생성한다.
    const radius = 1;
    const widthSegments = 6;
    const heightSegments = 6;
    const sphereGeometry = new THREE.SphereBufferGeometry(
      radius, widthSegments, heightSegments);
    )

    // material(재질)은 각각 생성한다.
    const sunMaterial = new THREE.MeshPhongMaterial({emissive: 0xFFFF00});
    const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial);
    sunMesh.scale.set(5, 5, 5); // 태양의 크기를 키운다
    scene.add(sunMesh);
    objects.push(sunMesh);
  }
 ```
 git push origin master test