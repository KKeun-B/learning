# Accelerating Wave equation 2 코드에서 가속화를 위해 사용된 코드(방법)
*	블록, 스레드 개수 최적화
>	병렬연산에 가장 최적화할 수 있도록 GPU의 SM과 warp를 계산하여 block과 thread 개수에 적용시킨다.  

>	사용된 구문
>*	int deviceId;
>*	cudaGetDevice(&deviceId);
>* cudaDeviceProp props;
>* cudaGetDeviceProperties(&props, deviceId);
>*	int multiProcessorCount = props.multiProcessorCount;
>*	int warpSize = props.warpSize
>*	size_t threads_per_block = warpSize*32;
>*	size_t number_of_blocks = multiProcessorCount*32;

*	메모리 프리패칭
>	페이지 폴트와 메모리 마이그레이션으로 인한 오버헤드를 줄이기 위해 사용되는 기법으로 메모리 전달 빈도를 낮춰 계산 속도를 높이는 기법이다.
>	CPU => GPU: deviceId
>	GPU => CPU: cudaCpuDeviceId

>	사용된 구문
>*	cudaMemPrefetchAsync(U, size, deviceId);
>*	cudaMemPrefetchAsync(U_new, size, deviceId);
>*	cudaMemPrefetchAsync(U, size, cudaCpuDeviceId);
>*	cudaMemPrefetchAsync(U_new, size, cudaCpuDeviceId);
