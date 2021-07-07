# WebGL 기본

WebGL에 대한 스터디 저장소 입니다.

> 참고자료: [https://webglfundamentals.org/webgl/lessons/ko/](https://webglfundamentals.org/webgl/lessons/ko/)

> 공식사이트: [https://www.khronos.org/webgl/](https://www.khronos.org/webgl/)




<br/><hr/><br/>



## 00. 용어정리

* ``보간``: 두 점을 연결하는 방법(직선, 곡선)에서 그 과정에 대한 샘플 데이터를 얻는 방법을 말합니다. (두 점을 연결하는 일련의 과정)



<br/><hr/><br/>



## 01. WebGL 이란?

``WebGL``은 GPU를 사용하는 ``Rasterization 엔진`` 입니다.

``Raster``란 비트맵을 말하며, 비트맵은 ``RGB``로 구성된 ``Pixel``들이 모여 만들어진 이미지를 말합니다.

즉, ``Rasterization 엔진``은 개발자가 작성한 코드를 비트맵으로 그려주는 엔진을 말합니다.

<br/>

``Rasterization 엔진``의 기본 동작원리는 두개의 ``함수 쌍``을 호출하여, 화면에 이미지를 출력하는 방식입니다.

호출하는 ``함수 쌍``은 다음과 같습니다.

* ``Vertex Shader``
* ``Fragment Shader``

<br/>

### 01-01. ``Vertex Shader``

``Rasterization 엔진``이 이미지를 화면에 출력할 때, 호출하는 함수는 ``Vertex Shader`` 입니다.

``Vertext Shader``의 기능은 출력할 도형이나 이미지 등의 코드를, 이미지로써 화면에 ``출력할 정점을 계산`` 하는것 입니다.


<br/><br/>


### 01-02. ``Fragment Shader``

``Vexter Shader``에서 이미지를 출력할 정점을 계산한 후에 ``Vexter Shader``에서 ``Fragment Shader``를 호출 합니다.

``Fragment Shader``는 각 ``Pixel``의 ``RGB``값을 계산하는 기능을 하며, ``Fragment Shader``가 완료 되어야 비로소 개발자가 명령한 코드가 화면에 이미지로 출력 됩니다.


<br/><br/>


### 01-03. ``gl.drawArrays``와 ``gl.drawElements``

위에서 언급했던 ``Vertex Shader``와 ``Fragment Shader``가 준비 되었다면, ``WebGL``을 사용하여 화면에 그리도록 실행시킬 수 있습니다.

``WebGL``의 API는 ``WebGL``을 실행하기 위한 ``상태관리``에 대한 것으로 구성되어 있습니다.

화면에 그리고자 하는 모양을 ``WebGL API``로 ``상태설정``을 한 후, ``WebGL``을 실행하면 원하는 이미지가 출력 됩니다.

<br/>

``WebGL``을 실행시키는 메서드가 바로 ``gl.drawArrays`` 또는 ``gl.drawElements`` 이며, 이 메서드들은 ``GPU``에서 ``Shader``를 실행하여 동작하게 됩니다.


<br/><br/>


### 01-04. 정리

``WebGL``은 무엇이고, 어떻게 실행되는가에 대해 정리해 보겠습니다.

* ``WebGL``은 ``GPU``를 사용하여 웹에 ``비트맵(이미지)``를 출력하는 ``Rasterization 엔진`` 입니다.
* ``GPU``가 화면을 그리기 위해서는 ``WebGL``에 의해 호출될 때, ``Vertex Shader``와 ``Fragment Shader``를 전달 받아야 합니다.
* ``Vertex Shader``는 화면에 출력하기 위해 작성한 코드를, 이미지로 ``화면에 출력할 정점``을 계산하는 기능을 합니다.
* ``Fragment Shader``는 ``Vertex Shader``에서 호출되며, 각 ``Pixel``의 ``RGB``값을 계산하는 기능을 합니다.



<br/><hr/><br/>



## 02. ``Shader``가 데이터를 받을 수 있는 방법

매번 새로운 이미지를 만들고 그리면, 부드러운 애니메이션이 되지 않습니다.

``보간``으로 변형된 ``데이터``를 받아서 출력해야 부드러운 움직임이 되는데, ``Shader``가 이 데이터를 받는 방법은 다음 4가지가 있습니다.

1. ``Attribute`` & ``Buffer``
    * ``Buffer``에는 출력할 이미지의 위치, 법선, 좌표, 색상과 같은 데이터를 담을 수 있습니다.
    * ``Attribute``는 ``Buffer``에 담긴 데이터를 가져오고 ``Vertex Shader``에 제공할 때 사용합니다.
    * ``Attribute``에서 ``Buffer``의 데이터를 임의로 가져올 수는 없고, ``Vertex Shader``가 실행될때마다 ``Buffer``에서 ``Attribute``에 데이터를 할당해 줍니다.

<br/>

2. ``Uniform``
    * ``Shader Program``을 실행하기 전에 설정하는 전역변수 개념 입니다.

<br/>

3. ``Texture``
    * ``Shader Program``에서 임의로 접근할 수 있는 데이터 배열 입니다.

<br/>

4. ``Varying``
    * ``Vertex Shader``가 ``Fragment Shader``에 데이터를 넘기는 방법 입니다.
    * ``Varying`` 데이터는 ``Fragment Shader``가 실행되는 동안 ``보간``됩니다.



<br/><hr/><br/>



## 03. WebGL 초기화

``WebGL``에서 ``GPU``에 그리기 명령을 하기 위해서는 ``Vertex Shader``와 ``Fragment Shader``를 ``WebGL``에 제공해야 합니다.

``Vertex Shader``는 ``정점``을 계산하기 위한 데이터를 제공해야 하는데, 이 데이터는 ``<canvas>``의 크기와 상관없이, ``(-1, -1) ~ (1, 1)``의 값을 가질 수 있습니다.

``Fragment Shader``는 색상을 계산하기 위한 데이터로, ``(0, 0) ~ (1, 1)``의 값을 가질 수 있습니다.



<br/><hr/><br/>



### 03-01. WebGL 초기화 - ``WebGL`` 객체 생성하기

script에서 ``<canvas>``요소를 사용하여 ``WegGL`` 컨텍스트 객체를 만들어야 합니다.

```html
<!-- 경로: 01-WebGL-기본.html -->
<body>
  <canvas id="myCanvas"></canvas>

  <script>
    const myCanvas = document.querySelector("#myCanvas");
    const gl = myCanvas.getContext("webgl");

    if(!gl) {
      alert("WebGL 객체 생성 실패");
    }
  </script>
</body>
```


<br/><br/>


### 03-02. WebGL 초기화 - ``Shader`` 생성 함수 만들기

``<canvas>``를 사용하여 생성한 ``gl``객체에서 사용할 ``Vertex Shader``와 ``Fragment Shader``를 생성하는 함수를 만들어야 합니다.

```html
<body>
  <canvas id="myCanvas"></canvas>

  <script>
    const myCanvas = document.querySelector("#myCanvas");
    const gl = myCanvas.getContext("webgl");

    if(!gl) {
      alert("WebGL 객체 생성 실패");
    }

    // Shader 생성 함수
    function createShader(gl, type, source) {
      // Shader 객체 생성
      const shader = gl.createShader(type);
      // Shader에 source 올리기
      gl.shaderSource(shader, source);
      // Shader를 만들기 위해 ``GLSL`` 컴파일 실행
      gl.compileShader(shader);

      // 컴파일 결과물 가져오기
      const success = gl.getShaderParameter(shader, gl.COMPILE_STATUS);

      if(success) {
        return success;
      }

      console.log(gl.getShaderInfoLog(shader));
      gl.deleteShader(shader);

      return null;
    }
  </script>
</body>
```


<br/><br/>


### 03-03. WebGL 초기화 - ``Vertex Shader`` 초기화 문자열(``GLSL``) 만들기

``Shader``를 만들기 위해서는 ``GLSL(Graphics Library Shader Language)``를 작성해야 합니다.

Javascript에서 만들 수 있는 ``문자열`` 형태로 ``GLSL``을 작성하여 제공해야 하는데, ``<script type="notjs">`` 처럼 Javascript가 아닌 ``<script>``에서도 만들 수 있습니다.

다음은 ``Vertex Shader``를 만들기 위한 ``GLSL`` 코드 입니다.

```html
<body>
  <script type="notjs">
    // 버퍼에서 받은 데이터
    attribute vec4 a_position;

    // Vertex Shader 실행 함수
    void main() {
      // gl_Position은 Vertex Shader가 담당하는 설정의 ``특수변수`` 입니다.
      gl_Position = a_position;
    }
  </script>
</body>
```

<br/>

위에서 만든 ``Vertex Shader Source``는 우리가 이전에 만든 ``createShader(gl, type, source)`` 함수를 사용하여 ``컴파일``을 해야 사용할 수 있습니다.

다음 코드는 ``Vertex Shader Source``를 컴파일 하는 코드 입니다.

```html
<body>
  <script>
    // Vertex Shader Source 가져오기
    const vertexShaderSource = document.querySelector("#vertex-shader-2d");
    // Vertex Shader 컴파일 및 생성
    const vertexShader = createShader(gl, gl.VERTEX_SHADER, vertexShaderSource);    
  </script>
</body>
```


<br/><br/>


### 03-04. WebGL 초기화 - ``Fragment Shader`` 초기화 문자열(``GLSL``) 만들기

``Vertex Shader``를 만들 방식과 동일하게 ``GLSL``을 문자열 형태로 만들어 ``Fragment Shader``를 만들 수 있습니다.

``GLSL``작성 시, ``Vertex Shader``와 다른 점은, ``Fragment Shader``에는 정밀도 설정이 있는데, 초기값이 설정되어 있는 않아서 직접 초기 정밀도를 설정해 주어야 합니다.

```html
<body>
  <script type="notjs">
    // 정밀도 설정값인 ``mediump``는 중간 정밀도 설정 입니다. (가장 무난한 설정)
    precision mediump float;

    // Fragment Shader 실행 함수
    void main() {
      // 붉은 보라색으로 설정
      gl_FragColor = vec4(1, 0, 0.5, 1);
    }
  </script>
</body>
```

<br/>

``Vertex Shader``와 마찬가지로, ``Fragment Shader``도 ``컴파일``을 해야 합니다.

```html
<body>
  <script>
    // Fragment Shader Source 가져오기
    const fragmentShaderSource = document.querySelector("#fragment-shader-2d");
    // Fragment Shader 컴파일 및 생성
    const fragmentShader = createShader(gl, gl.FRAGMENT_SHADER, fragmentShaderSource);
  </script>
</body>
```


<br/><br/>


### 03-05. WebGL 초기화 - Shader 정리

지금까지 설정한 초기화는 다음과 같습니다.

* WebGL 객체 생성
* Shader 생성 함수 만들기
* Vertex Shader Source 작성
* Fragment Shader Source 작성
* Vertex Shader 컴파일 및 생성
* Fragment Shader 컴파일 및 생성

<br/>

위의 작업을 통합하면 다음과 같습니다.

```html
<body>
  <canvas id="myCanvas"></canvas>

  <!-- Vertex Shader Source -->
  <script type="notjs">
    // Buffer 에서 데이터 가져오기
    attribute vec4 a_position;

    // Vertex Shader 메인 함수
    void main() {
      gl_Position = a_position;
    }
  </script>

  <!-- Fragment Shader Source -->
  <script type="notjs">
    // 정밀도를 mediump(중간 정밀도)로 설정
    precision mediump float;

    // Fragment Shader 메인 함수
    void main() {
      // 붉은 보라색으로 설정
      gl_FragColor = vec4(1, 0, 0.5, 1);
    }
  </script>

  <script>
    const myCanvas = document.querySelector("#myCanvas");
    const gl = myCanvas.getContext("webgl");

    if(!gl) {
      alert("WebGL 생성 실패");
    }

    // Shader 컴파일 및 생성 함수 만들기
    function createShader(gl, type, source) {
      const shader = gl.createShader(type);
      gl.shaderSource(shader, source);
      gl.compileShader(shader);

      // 컴파일 결과 Shader 반환
      const success = gl.getShaderParameter(gl, gl.COMPILE_STATUS);
      if(success) {
        return success;
      }

      console.log("Shader 생성 실패");
      gl.deleteShader(shader);
    }
  </script>
</body>
```


<br/><br/>


### 03-06. WebGL 초기화 - ``Program`` 생성하기

이제 ``Program`` 객체를 생성하면 ``WebGL``을 사용하여 ``GPU``로 이미지를 그릴 수 있게 됩니다.

위에서 만들었던 ``Vertex Shader``와 ``Fragment Shader``를 사용하여 ``Program``을 만들 수 있는데, 이를 실행해줄 함수 ``createProgram()``을 작성해야 합니다.

```html
<body>
  <script>
    function createProgram(gl, vertexShader, fragmentShader) {
      // Program 객체 생성
      const program = gl.createProgram();
      // Program에 VertexShader 붙이기
      gl.attachShader(program, vertexShader);
      // Program에 FragmentShader 붙이기
      gl.attachShader(program, fragmentShader);
      // WebGL에 Program 연결
      gl.linkProgram(program);

      // WebGL에 연결한 Program객체 반환하기
      const succes = gl.getProgramParameter(program, gl.LINK_STATUS);

      if(success) {
        return success;
      }

      console.log("Program 생성 실패");
      gl.deleteProgram(program);
    }

    // Program 생성
    const program = createProgram(gl, vertexShader, fragmentShader);
  </script>
</body>
```


<br/><br/>


### 03-07. WebGL 초기화 - ``Bind Point`` 지정하기

WebGL API는 ``GPU``를 동작시키기 위한 ``상태설정``을 위한 기능을 제공합니다.

이 ``상태설정``은 우리가 ``Buffer``에 ``상태설정``을 하면, ``Vertex Shader``에서 ``attribute``로 데이터를 받아서 수행하게 됩니다.

때문에 우리는 ``Buffer``를 생성하여 ``WebGL``에 제공해주어야 ``Buffer``를 통해서 ``GPU``를 동작시킬 수 있습니다.

즉, ``Buffer``를 통해서 ``WebGL``이 ``GPU``를 동작 시키고, 이 ``Buffer``를 ``Bind Point``라고 합니다.

<br/>

다음 코드는 ``Bind Point``를 만들기 위해, ``Buffer``생성과 할당 입니다.

```html
<body>
  <script>
    // Buffer 생성
    const positionBuffer = gl.createBuffer();
    // WebGL에 Buffer 제공으로 Bind Point 형성
    gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

    // 형성된 Bind Point로 데이터 전달
    // 2D Point 3개 데이터
    const positions = [
      0, 0,
      0, 0.5,
      0.7, 0,
    ];

    gl.bufferData(
      // 전달할 Bind Point (ARRAY_BUFFER 또는 ELEMENT_ARRAY_BUFFER)
      gl.ARRAY_BUFFER,
      // WebGL에서 요구하는 데이터형으로 변환하여 전달
      new Float32Array(positions),
      // 이미지 변화가 적다는 것을 WebGL에게 미리 언질하기
      gl.STATIC_DRAW,
    );
  </script>
</body>
```

<br/>

지금까지의 과정은 ``WebGL``을 사용하여 ``GPU``를 동작시키기 위한 ``초기화`` 였습니다.



<br/><hr/><br/>



## 04. WebGL 렌더링

지금부터 작성할 코드는 화면에 그리는 동작마다 실행될 코드 입니다.


<br/><br/>

### 04-01. WebGL 렌더링 - ``<canvas>``와 WebGL의 ``Pixel수`` 동기화

``<canvas>``요소의 실제 화면에 출력할 크기는 ``CSS의 width, height``에 의해 결정 됩니다.

때문에 ``WebGL``에서 그리고자 하는 영역의 ``Pixel수``를 ``<canvas>``의 실제 크기와 동기화 해주어야 이미지가 도트로 깨지는 현상을 막을 수 있습니다.

그리고, ``VertexShader``의 ``Clip Space``영역도 ``Screen Space``와 동기화 시켜주어야 합니다.

```html
<body>
  <script>
    function resizeCanvasToDisplaySize(canvas) {
      const displayWidth = canvas.clientWidth;
      const displayHeight = canvas.clientHeight;

      const needResize = 
          canvas.width !== displayWidth ||
          canvas.height !== displayHeight;

      if(needResize) {
        canvas.width = displayWidth;
        canvas.height = displayHeight;
      }
    }

    // <canvas>의 실제 크기와 WegGL의 출력 Pixel수 동기화
    resizeCanvasToDisplaySize(gl.canvas);

    // VertexShader의 ClipSpace와 ScreenSpace를 동기화
    gl.viewport(0, 0, gl.canvas.width, gl.canvas.height);
  </script>
</body>
```


<br/><br/>


## 04-02. WebGL 렌더링 - 화면 지우기

아직 출력한 이미지는 없지만, 초기화 의미로 화면을 지워 보겠습니다.

화면을 지우는 방식은, 단일 색상을 사용하여 ``<canvas>`` 전체영역츨 ``Masking``하는 방식 입니다.

```html
<body>
  <script>
    // 화면을 지울 때 사용할 색상 설정
    gl.clearColor(0, 0, 0, 0);

    // 화면 지우기 실행
    // gl.clearColor()에서 설정한 색상으로 Masking 실행
    gl.clear(gl.COLOR_BUFFER_BIT);
  </script>
</body>
```


<br/><br/>


## 04-03. WebGL 렌더링 - ``Program`` 활성화

``WebGL 초기화``단계에서 생성했던 ``Program``객체는 ``WebGL``에 등록만 해준 상태입니다.

이 ``Program``을 사용하려면, ``WebGL``에 ``사용할 Program``을 알려주어야 합니다.

```html
<body>
  <script>
    // 앞으로 실행할 Program 설정
    gl.useProgram(program);
  </script>
</body>
```


<br/><br/>


### 04-04. WebGL 렌더링 - ``Attribute`` 활성화

초기화 단계에서 ``VertexShader Source``에서 ``Attribute``를 하나 만들었습니다.

이 ``Attribute``는 ``Buffer``에서 전달해주는 데이터를 받는 역할을 하게 됩니다.

``GLSL``로 작성한 ``VertexShader Source``에서 선언했던 ``a_position``은 선언은 됬지만, ``활성화``를 시켜주지 않으면 사용되지 않습니다.

다음 코드는 ``Attribute``를 ``활성화`` 시켜줍니다.

```html
<body>
  <script>
    const positionAttributeLocation = gl.getAttribLocation(program, "a_position");

    gl.enableVertexAttribArray(positionAttributeLocation);
  </script>
</body>
```


<br/><br/>


### 04-05. ``Buffer`` 동작방식 설정

위에서 활성화 시킨 ``Attribute``를 ``Buffer``에 연결시키고, ``Buffer``의 동작방식을 설정해야 합니다.

다음 코드는 ``Buffer``의 동작설정 코드 입니다.

```html
<body>
  <script>
    const positionAttributeLocation = gl.getAttribLocation(program, "a_position");
    
    // Buffer가 한번 동작할 때 Attribute에 넘겨줄 데이터 개수
    const size = 2;
    // Attribute에 넘겨줄 데이터 타입
    const type = gl.FLOAT;
    // 데이터 정규화 안함 설정
    const normalize = false;
    // 데이터를 꺼내는 단위 크기 (보폭)
    const stribe = 0;
    // Buffer의 시작점
    const offset = 0;

    // 위의 설정값을 사용하여, Buffer에 적용 및 Attribute에 Buffer 바인딩
    gl.vertexAttribPointer(
      positionAttributeLocation,
      size,
      type,
      normalize,
      stribe,
      offset,
    );
  </script>
</body>
```


<br/><br/>


### 04-06. 삼각형 그리기

이제 렌더링 준비까지 완료 되었습니다.

``WebGL``에 렌더링을 실행 시킵니다.

```html
<body>
  <script>
    // 도형 타입
    const primitiveType = gl.TRIANGLES;
    // 그리기 오프셋
    const drawingOffset = 0;
    // Buffer의 동작 횟수
    const drawingCount = 3;

    gl.drawArrays(primitiveType, drawingOffset, drawingCount);
  </script>
</body>
```



<br/><hr/><br/>



## 05. 총 정리

지금까지 작성했던 ``WebGL`` 초기화와 렌더링 과정을 정리하면 다음과 같습니다.

* 초기화 단계
    1. ``<canvas>`` 만들기
    2. ``VertexShader Source`` 만들기 (GLSL)
    3. ``FragmentShader Source`` 만들기 (GLSL)
    4. ``VertexShader`` 생성하기
    5. ``FragmentShader`` 생성하기
    6. ``Program`` 생성하기
    7. ``Buffer`` 생성 및 ``WebGL``에 연결하기
    8. ``Buffer``에 데이터 입력하기 (화면에 그릴 점 위치값)
    9. ``VertexShader``의 ``Clip Space``와 ``<canvas>``의 ``Pixel수`` 동기화
    10. ``<canvas>`` 초기화 (Masking)
    11. ``VertexShader``의 ``Attribute`` 경로값 가져오기
    12. ``Attribute`` 경로를 사용하여 ``Buffer``에 연결 및 ``Buffer`` 실행방식 설정
    13. ``WebGL``의 그리기 함수 실행
