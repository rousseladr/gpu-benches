NVCC := nvcc

# internal flags
NVCCFLAGS   := -std=c++11 -O3 -arch=sm_90 --compiler-options="-O2 -pipe -march=native -Wall -fopenmp" -Xcompiler -rdynamic --generate-line-info
CCFLAGS     := 
LDFLAGS     := -lcublas -lnvidia-ml
NAME 		:= latency
PREFIX		:=
N 			:= 1

all: $(PREFIX)cuda-$(NAME) $(PREFIX)cuda-$(NAME)-cudamalloc $(PREFIX)cuda-$(NAME)-malloc $(PREFIX)cuda-$(NAME)-malloc-host $(PREFIX)cuda-$(NAME)-malloc-device-close $(PREFIX)cuda-$(NAME)-malloc-device-remote

$(PREFIX)cuda-$(NAME): main.cu Makefile
	$(NVCC) $(NVCCFLAGS) $(INCLUDES) -o $@ $< $(LDFLAGS)

$(PREFIX)cuda-$(NAME)-malloc: main.cu Makefile
	$(NVCC) $(NVCCFLAGS) $(INCLUDES) -DMALLOC -o $@ $< $(LDFLAGS)

$(PREFIX)cuda-$(NAME)-malloc-host: main.cu Makefile
	$(NVCC) $(NVCCFLAGS) $(INCLUDES) -DMALLOC -DHOST -o $@ $< $(LDFLAGS)

$(PREFIX)cuda-$(NAME)-malloc-device-close: main.cu Makefile
	$(NVCC) $(NVCCFLAGS) $(INCLUDES) -DMALLOC -DCLOSE_DEVICE -o $@ $< $(LDFLAGS)

$(PREFIX)cuda-$(NAME)-malloc-device-remote: main.cu Makefile
	$(NVCC) $(NVCCFLAGS) $(INCLUDES) -DMALLOC -DREMOTE_DEVICE -o $@ $< $(LDFLAGS)

$(PREFIX)cuda-$(NAME)-cudamalloc: main.cu Makefile
	$(NVCC) $(NVCCFLAGS) $(INCLUDES) -DCUDAMALLOC -o $@ $< $(LDFLAGS)

main.hip: main.cu
	hipify-perl main.cu > main.hip

$(PREFIX)hip-$(NAME): main.hip Makefile ../rocm-metrics/rocm-metrics.hpp
	echo $(HIP_HOME)
	hipcc -std=c++20 -I$(HIP_HOME)/include/rocprofiler/ -I$(HIP_HOME)/hsa/include/hsa -L$(HIP_HOME)/rocprofiler/lib -lrocprofiler64 -lhsa-runtime64 -lrocm_smi64 -ldl -o $@ $<

clean:
	rm -f cuda-$(NAME) hip-$(NAME)
	rm -f cuda-$(NAME)-*
	rm -f *-cuda-$(NAME) *-hip-$(NAME)
	rm main.hip
