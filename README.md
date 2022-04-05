# CUDA on Google Colab
The cuda code is mainly for nvidia hardware device. In this repo, I will show how to run your cuda c or cuda cpp code on the google colab platform for free.

## How to run the cuda code on Google Colab
You can follow the steps below. The specific code is inside the [example.ipynb](./example.ipynb) file.  
### a. Setting the environment
1. The Google Colab is initialized with no hardware as default. In that case, we need to first set our hardware to GPU.  
```
Runtime > Change runtime type > Setting the Hardware accelerator to GPU > Save
```
2. If we need to use the cuda, we have to have cuda tookit. The Google Colab has already installed that. We can use the following code to verify that. (The output will show the file under the /usr/local, which has several different version of cuda.)  
```bash
!ls /usr/local/
```  
3. Then, we need to verify if the nvcc command is there in the $PATH environment to use. We can use the following code to verify that. It will show the full path of nvcc command.  
```bash
!which nvcc
``` 
4. The cuda driver is also important for us to use the cuda. The Google Colab also has already installed that. We can use the following code to verify that. It will show the property of the nvidia card, which means the driver is working properly.**Remember the NVIDIA device you are using now, in my case, I use K80**  
```bash
!nvidia-smi
```
### b. Write, Compile and Run the program.
1. Write cuda code  
```cuda
%%writefile hello.cu

#include<stdio.h>
__global__ void hello(void)
{
    printf("GPU: Hello!\n");
}
int main(int argc,char **argv)
{
    printf("CPU: Hello!\n");
    hello<<<1,10>>>();
    cudaDeviceReset();
    return 0;
}
```  
2. Compile the code (**It depends on the device, K80 use the Kepler arch which only supports CUDA 5 to CUDA 10. But the Google Colab has CUDA 11 as default. There are two ways to fix that. First is adding flag, sm_37 is for K80. Second is create a soft link to make the CUDA11 to CUDA10**)  
[NVIDIA arch](https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/)  
[GPU Compute Capability](https://developer.nvidia.com/cuda-gpus#compute)
```bash
# Method 1
!nvcc -arch=sm_37 -gencode=arch=compute_37,code=sm_37 hello.cu -o hello

# Method 2
!rm -rf /usr/local/cuda
!ln -s /usr/local/cuda-10.1 /usr/local/cuda
!nvcc hello.cu -o hello
```  
3. Run the program
```bash
!./hello
```  
If you see 1 cpu hello and 10 gpu hello in the output. The program is working well.

## Reference
1. [Cuda not working on Google Colab](http://wiki.alcidesfonseca.com/blog/cuda-not-working-google-collab/)
2. [Matching CUDA arch and CUDA gencode for various NVIDIA architectures](https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/)
3. [GPU Compute Capability](https://developer.nvidia.com/cuda-gpus#compute)
