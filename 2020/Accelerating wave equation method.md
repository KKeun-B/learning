*	Wave equation 코드에서 가속화를 위해 사용된 코드(방법)

>	\_\_global__ 선언
>*	해당 함수가 GPU에서 실행되도록 하고 전역적으로 호출될 수 있도록 하는 코드로써 \_\_global__ 선언이 된 함수는 반드시 void 타입을 리턴해야한다.

>	사용된 구문
>*	\_\_global__ void Hyperbolic(float* U, float* U_new, int CFL, int N)
>*	\_\_global__ void Init(float* U,float* U_new, int N, int iter, float dx)

*	스레드, 블록 인덱스와 블록, 그리드 디멘션
>*	커널 내부 정의에서 CUDA 제공 변수는 실행 스레드, 블록 및 그리드를 표현한다.  
	![image](https://user-images.githubusercontent.com/83944915/118295109-73b3c000-b516-11eb-9e78-93d0eddb7d02.png)
>*	gridDim.x : 그리드 내의 블록 수를 의미한다. 위의 그림의 경우, 초록색으로 표시된 부분이 블록이므로 2를 출력한다.
>*	blcokDim.x : 한 블록내의 스레드 개수를 의미한다. 그리드 내의 모든 블록은 같은 수의 스레드를 갖는다. 위의 그림에서 하얀색으로 표시된 부분이 스레드이므로 한 블록내의 스레드 개수인 4를 출력한다.
>*	threadIdx.x : 각 블록 내부 스레드의 인덱스를 의미한다. 각 스레드에는 0부터 시작하는 인덱스가 할당되며 주소로써 이해될 수 있다.
>*	blockIdx.x : 각 블록의 인덱스를 의미한다. 스레드와 같이 각 블록에는 0부터 시작하는 인덱스가 할당되며 주소로써 이해될 수 있다.
>*	병렬화 수준을 높이기 위해 Idx와 Stride 변수를 선언하여 그리드 폭 반복문(grid_stride loop)에서 사용한다.
>*	NVIDIA 아키텍쳐의 한계로 하나의 블록에 스레드는 최대 1024개 까지만 수용 가능하다. 이 이상 스레드를 설정하면 오류가 나게 된다.

>	사용된 구문
  >*	Idx = threadIdx.x + blockIdx.x * blockDim.x
  >*	Stride = gridDim.x + blockDim.x
  >*	size_t threads_per_block = 256
  >*	size_t number_of_blocks = (N + threads_per_block - 1) / threads_per_block

*	For 반복문 가속
>	그리드 폭 반복문(grid_stride loop)를 이용해 for문을 병렬 계산 및 가속화한다.  

>	사용된 구문
>*	for (int i = idx+1; i < N - 1; i+=stride)
>*	for (int i = idx; i < N - 1; i+=stride)
>*	for (int i = (int)((50) / dx)+idx; i < (int)((110.0 / dx) + 1); i+=stride)


*	GPU와 CPU가 사용하는 메모리 할당
>	malloc 명령어로 메모리 할당을 하면 GPU에서 사용할 수 없기 때문에 cudaMallocManaged 명령어를 사용해야한다.

>	사용된 구문
>*	cudaMallocManaged(&U, size)
>*	cudaMallocManaged(&U_new, size);


*	병렬 커널 구동하기
>	\_\_global__ 선언이 된 함수를 이용하기 위해서는 “함수이름 <<<사용하는 블록 수, 사용하는 스레드 수>>>(사용하는 변수)”의 형태를 맞춰줘야 한다.  
>	스레드의 개수는 1024개를 넘을 수 없으므로 유의해야 하고 블록 수 역시 적절하게 설정해야 한다.

>	사용된 구문
>*	Init<<<number_of_blocks, threads_per_block>>>(U, U_new, N, iter, dx)
>*	Hyperbolic<<<number_of_blocks, threads_per_block>>>(U, U_new, CFL, N)


*	커널 구동 동기화
>	대부분의 C++ 코드와는 다르게 비 동기적이기 때문에 \_\_global__선언을 통해 GPU에서 병렬연산을 하도록 해도 커널 구동을 동기화 시켜주지 않는다면 CPU코드가 GPU 연산을 무시하여 해당 커널이 작동하지 않게 된다.  
>	커널 구동 동기화를 하면 CPU가 GPU연산이 완료될 때까지 대기하며 GPU연산에서 필수적인 코드이다. 이때 사용하는 코드가 cudaDeviceSynchronize()이다.

>	사용된 구문
>*	cudaDeviceSynchronize()


*	GPU와 CPU가 사용하는 메모리 할당 해제
>	cudaMallocManaged를 통해 CPU와 GPU에서 사용할 수 있도록 한 메모리 할당은 코드 실행이 끝나기 전에 반드시 해제를 해주어야 한다.  
>	malloc과 대응하는 것이 free라면 cudaMallocManaged와 대응하는 것은 cudaFree()이다.

>	사용된 구문
>*	cudaFree(U)
>*	cudaFree(U_new)


*	가속화 CUDA 코드 컴파일 및 실행
>	CUDA 플랫폼을 컴파일 하기 위해 필요한 명령어로써 !nvcc가 사용된다.  
>	.cu 프로그램을 컴파일 하며 -o는 출력파일 이름 지정을 -run은 컴파일된 바이너리를 자동으로 실행하게 한다.

>	사용된 구문
>*	!nvcc -o hw5 hw5.cu -run

