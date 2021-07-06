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

``보간``에 의해 변형된 ``데이터``를 받아서 출력해야 부드럽운 움직임이 되는데, ``Shader``가 이 데이터를 받는 방법은 다음 4가지가 있습니다.

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



## 03. 