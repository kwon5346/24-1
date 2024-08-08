###  DX11 초기화 및 ImGui

---

#### 헤더파일 

- d3d11.h : Direct3D관련 헤더들을 바로 불러올 수 있다.
- D3compiler.h : VS.hlsl(vertex shader hlsl)과 PS.hlsl(pixel shader hlsl)을 컴파일할때 사용 (hlsl은 high-level shader language)



####  구현한 내용

![image](https://github.com/user-attachments/assets/4858a8b5-cec6-42f6-b8fc-34553442875c)


- 삼항 연산자를 이용하여 i가 증가할 때마다 pixelColor를 red, green, blue로 변하면서 픽셀 한칸씩 이동하도록 Update함수 구현

![image](https://github.com/user-attachments/assets/9860a05c-f56f-499c-ade9-4d7983bf4a60)

- imGui의 SliderFloat3 함수에서 canvasColor를 변경하기위해 example class에 canvasColor 배열 선언

![image](https://github.com/user-attachments/assets/e6d3f084-8330-4805-a159-22cb223786f6)


- 'Imgui::SliderFloat3("RGB(0.0)-> 1.0", example->canvasColor, 0.0f, 1.0f)'로 배경 RGB 색상 값을 조정하는 슬라이더를 생성한다.
- example->Update()에서는 그릴 것들을 메모리에 업데이트 해주고
- example->Render()에서 실제로 그리고,
- example->swapChain->Present(1,0)에서 그린 것들을 화면에 보여주는 순서이다.

- swapChain이란 메모리에다 그림을 그리고 메모리에 있는것을 번갈아가며 화면에 쏴주는 것이다.

  - GPU는 비디오 메모리에 존재하는 프레임 버퍼에 이미지를 계속해서 지우고 그리는 과정을 반복한다.

  - 비디오 컨트롤러는 이 프레임 버퍼를 참조하여 스크린에 이미지를 출력하는데, 이 속도가 GPU가 프레임 버퍼에 이미지를 그리는 속도보다 훨씬 빠르다.

  - 그래서 프레임 버퍼가 갱신되는 과정이 스크린에 모두 그려지면서 이미지 전체가 사라졌다 다시 그려지는 것처럼 깜빡거림(Flickering)이 생길 수 있다.

  - 이 현상을 방지하기 위해 전면 버퍼와 후면 버퍼를 추가적으로 두는데, 이 방식을 다중 버퍼링이라 하고, 그중 2개의 버퍼만 사용하는 방식을 이중 버퍼링이라고 한다.

    

    1.  GPU는 후면 버퍼에 이미지를 그린다.

    2. 후면 버퍼에 그리기가 완료되면 전면 버퍼와 후면 버퍼를 교환한다.

    3. 비디오 컨트롤러가 해당 전면 버퍼를 참조해 스크린을 갱신한다.

       

  - 전면 버퍼와 후면 버퍼와 후면 버퍼의 포인터만 교환하도록 해서 효율적인 구현이 가능하다.

![img](https://blog.kakaocdn.net/dn/cCm1pr/btraqRG9rXR/38m9mFLv3wjm46p4pI4O20/img.png)

- 포인터가 연속적으로 교체되는 모양이 사슬같아서 DirectX에서는 Swap Chain이라고 부르고,
- 위와 같이 전면 버퍼와 후면 버퍼의 포인터를 교환하는 작업을 Present라고 부른다.



####  구현 결과

![image](https://github.com/user-attachments/assets/980fd357-df3f-4324-bf46-75204c667f7c)

- DirectX는 속도를 높이기 위해서 그래픽스 파이프라인을 여러 단계로 쪼개었다. 기본적으로 vertext shader와 pixel shader를 사용하고 더 고급 기능을 구현할땐 다른 여러가지 shader를 사용할 수 있다. (Compute shader, Realtime Raytracing을 사용하기위한 여러 가지 쉐이더 등)
- DX의 실시간 그래픽스 파이프라인은 Rasterization을 기본적으로 사용한다.



####  궁금증

- struct Vectex는 3차원 좌표를 표현하는데 왜 크기 4짜리 Vec4를 사용할까?
- vertext shader와 pixel shader의 차이가 무엇일까
