##############################################################################
#
# CUDA Data Parallel Primitives (CUDPP) CMake file
#
###############################################################################

include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/../../include
  ${CMAKE_CURRENT_SOURCE_DIR}/../../ext/moderngpu/include
  ${CMAKE_CURRENT_SOURCE_DIR}/../../ext/cub
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/app
  ${CMAKE_CURRENT_SOURCE_DIR}/kernel
  ${CMAKE_CURRENT_SOURCE_DIR}/cta
  )

set(CCFILES
  cudpp.cpp
  cudpp_plan.cpp
  cudpp_manager.cpp
  ${CMAKE_SOURCE_DIR}/ext/moderngpu/src/mgpuutil.cpp
  )

set (HFILES
  cudpp_manager.h
  cudpp_plan.h
  cuda_util.h
  cudpp_util.h
  cudpp_globals.h
  cudpp_compact.h
  cudpp_compress.h
  cudpp_listrank.h
  cudpp_sa.h
  cudpp_mergesort.h
  cudpp_multisplit.h
  cudpp_radixsort.h
  cudpp_rand.h
  cudpp_reduce.h
  cudpp_stringsort.h
  cudpp_scan.h
  cudpp_segscan.h
  cudpp_spmvmult.h
  sharedmem.h
  )

set (CUHFILES
  cta/compress_cta.cuh
  cta/mergesort_cta.cuh
  cta/radixsort_cta.cuh
  cta/rand_cta.cuh
  cta/scan_cta.cuh
  cta/segmented_scan_cta.cuh
  cta/stringsort_cta.cuh
  kernel/compact_kernel.cuh
  kernel/compress_kernel.cuh
  kernel/listrank_kernel.cuh
  kernel/sa_kernel.cuh
  kernel/mergesort_kernel.cuh
  kernel/multisplit_kernel.cuh
  kernel/radixsort_kernel.cuh
  kernel/rand_kernel.cuh
  kernel/reduce_kernel.cuh
  kernel/segmented_scan_kernel.cuh
  kernel/spmvmult_kernel.cuh
  kernel/stringsort_kernel.cuh
  kernel/vector_kernel.cuh
  kernel/tridiagonal_kernel.cuh
  )

set(CUFILES
  app/reduce_app.cu
  app/compact_app.cu
  app/compress_app.cu
  app/listrank_app.cu
  app/sa_app.cu
  app/mergesort_app.cu
  app/multisplit_app.cu
  app/scan_app.cu
  app/segmented_scan_app.cu
  app/spmvmult_app.cu
  app/stringsort_app.cu
  app/radixsort_app.cu
  app/rand_app.cu
  app/tridiagonal_app.cu
  ${CMAKE_SOURCE_DIR}/ext/moderngpu/src/mgpucontext.cu
  )

set(HFILES_PUBLIC
  ${CMAKE_SOURCE_DIR}/include/cudpp.h
  ${CMAKE_SOURCE_DIR}/include/cudpp_config.h
  )

source_group("CUDA Source Files" FILES ${CUFILES})
source_group("CUDA Header Files" FILES ${CUHFILES})

#set(GENCODE_SM12 -gencode=arch=compute_12,code=sm_12 -gencode=arch=compute_12,code=compute_12)
#set(GENCODE_SM13 -gencode=arch=compute_13,code=sm_13 -gencode=arch=compute_13,code=compute_13)
#set(GENCODE_SM20 -gencode=arch=compute_20,code=sm_20 -gencode=arch=compute_20,code=compute_20)
set(GENCODE_SM21 -gencode=arch=compute_20,code=sm_21 -gencode=arch=compute_20,code=compute_20)
set(GENCODE_SM30 -gencode=arch=compute_30,code=sm_30 -gencode=arch=compute_30,code=compute_30)
set(GENCODE_SM35 -gencode=arch=compute_35,code=sm_35 -gencode=arch=compute_35,code=compute_35)
set(GENCODE_SM37 -gencode=arch=compute_37,code=sm_37 -gencode=arch=compute_37,code=compute_37)
set(GENCODE_SM50 -gencode=arch=compute_50,code=sm_50 -gencode=arch=compute_50,code=compute_50)
set(GENCODE_SM60 -gencode=arch=compute_60,code=sm_60 -gencode=arch=compute_60,code=compute_60)

# read https://devblogs.nvidia.com/using-cuda-warp-level-primitives/. Cuda 9 and Volta introduced a new independent thread scheduling mechanism. 
set(GENCODE_SM70 -gencode=arch=compute_60,code=sm_70 -gencode=arch=compute_60,code=compute_60)
set(GENCODE_SM70_IN_SM60_COMPATIBILITY_MODE -gencode=arch=compute_60,code=sm_70 -gencode=arch=compute_60,code=compute_60)

#set(GENCODE -gencode=arch=compute_20,code=compute_20) # at least generate PTX

#option(CUDPP_GENCODE_SM12
#       "ON to generate code for Compute Capability 1.2 devices (e.g. GeForce GT 330M)"
#       OFF)

#option(CUDPP_GENCODE_SM13
#       "ON to generate code for Compute Capability 1.3 devices (e.g. Tesla C1060)"
#       OFF)

#option(CUDPP_GENCODE_SM20
#       "ON to generate code for Compute Capability 2.0 devices (e.g. Tesla C2050)"
#       OFF)

#option(CUDPP_GENCODE_SM21
#       "ON to generate code for Compute Capability 2.1 devices (e.g. NVS 4200M)"
#       OFF)

option(CUDPP_GENCODE_SM30
       "ON to generate code for Compute Capability 3.0 devices (e.g. Tesla K10)"
       ON)

option(CUDPP_GENCODE_SM35
       "ON to generate code for Compute Capability 3.5 devices (e.g. Tesla K20)"
       OFF)

option(CUDPP_GENCODE_SM37
       "ON to generate code for Compute Capability 3.7 devices (e.g. Tesla K80)"
       ON)

option(CUDPP_GENCODE_SM50
       "ON to generate code for Compute Capability 5.0 devices (e.g. GeForce GTX 750)"
       OFF)

option(CUDPP_GENCODE_SM60
       "ON to generate code for Compute Capability 6.0 devices (e.g. Pascal P100)"
       ON)

option(CUDPP_GENCODE_SM60_COMPATIBILITY_MODE
       "ON to generate code for Compute Capability > 6.0 in compatibility mode. using the Pascal thread scheduling.  read https://devblogs.nvidia.com/using-cuda-warp-level-primitives/."
       ON)

option(CUDPP_GENCODE_SM70
       "ON to generate code for Compute Capability 7.0 devices (e.g. Volta V100)"
       ON)

option(CUDA_VERBOSE_PTXAS 
       "ON for a verbose PTX assembler"
       OFF)

if (CUDPP_GENCODE_SM12)
    message( STATUS "Generating code for SM12 architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM12})
endif(CUDPP_GENCODE_SM12)

if (CUDPP_GENCODE_SM13)
    message( STATUS "Generating code for SM13 (Tesla C1060) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM13})
endif(CUDPP_GENCODE_SM13)

if (CUDPP_GENCODE_SM20)
    message( STATUS "Generating code for SM20 (Fermi) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM20})
endif(CUDPP_GENCODE_SM20)

if (CUDPP_GENCODE_SM30)
  message( STATUS "Generating code for SM30 (K10, generic Kepler) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM30})
endif(CUDPP_GENCODE_SM30)

if (CUDPP_GENCODE_SM35)
  message( STATUS "Generating code for SM35 (K40) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM35})
endif(CUDPP_GENCODE_SM35)

if (CUDPP_GENCODE_SM37)
  message( STATUS "Generating code for SM37 (K80) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM37})
endif(CUDPP_GENCODE_SM37)

if (CUDPP_GENCODE_SM50)
  message( STATUS "Generating code for SM50 (Maxwell) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM50})
endif(CUDPP_GENCODE_SM50)

if (CUDPP_GENCODE_SM60)
  message( STATUS "Generating code for SM60 ( Pascal P100 ) architecture" )
  set(GENCODE ${GENCODE} ${GENCODE_SM60})
endif(CUDPP_GENCODE_SM60)


if( CUDPP_GENCODE_SM60_COMPATIBILITY_MODE )

    # all code generation options using Pascal's thread scheduling in compatibility mode.

  if (CUDPP_GENCODE_SM70)
    message( STATUS "Building SM70 (Volta V100 architecture) code in compatibility mode : using Pascal thread scheduling" )
    set(GENCODE ${GENCODE} ${GENCODE_SM70_IN_SM60_COMPATIBILITY_MODE})
  endif(CUDPP_GENCODE_SM70)

else ( CUDPP_GENCODE_SM60_COMPATIBILITY_MODE )

  # all code generation options using Volta's nw indpendent thread scheduling.
  
  if (CUDPP_GENCODE_SM70)
    message( STATUS "Building SM70 (Volta V100 architecture) code using Volta's new independent thread scheduling" )
    set(GENCODE ${GENCODE} ${GENCODE_SM70})
  endif(CUDPP_GENCODE_SM70)

endif( CUDPP_GENCODE_SM60_COMPATIBILITY_MODE )

if (CUDA_VERBOSE_PTXAS)
  set(VERBOSE_PTXAS --ptxas-options=-v)
endif (CUDA_VERBOSE_PTXAS)

if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  exec_program(uname ARGS -v  OUTPUT_VARIABLE DARWIN_VERSION)
  string(REGEX MATCH "[0-9]+" DARWIN_VERSION ${DARWIN_VERSION})
  # message(STATUS "Detected Darwin kernel version: ${DARWIN_VERSION}")
  if ((DARWIN_VERSION GREATER 13) OR (DARWIN_VERSION EQUAL 13))
    set(APPLE_MAVERICKS_OR_LATER 1 INTERNAL)
    set(CMAKE_CXX_FLAGS -stdlib=libstdc++)
    set(CMAKE_C_FLAGS -stdlib=libstdc++)
    if(CUDA_VERSION_MAJOR EQUAL 6 AND CUDA_VERSION_MINOR LESS 5)
      set(CUDA_6_0 1)
    endif(CUDA_VERSION_MAJOR EQUAL 6 AND CUDA_VERSION_MINOR LESS 5)
    if(CUDA_VERSION_MAJOR LESS 6 OR CUDA_6_0)
      if(NOT CUDA_NVCC_FLAGS MATCHES "ccbin")
        set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} -ccbin;/usr/bin/clang)
      endif(NOT CUDA_NVCC_FLAGS MATCHES "ccbin")
    endif(CUDA_VERSION_MAJOR LESS 6 OR CUDA_6_0)
  endif ((DARWIN_VERSION GREATER 13) OR (DARWIN_VERSION EQUAL 13))
  # current CUDA on Mavericks uses libstdc++, not libc++
  # https://github.com/cudpp/cudpp/wiki/BuildingCUDPPWithMavericks
endif(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")

cuda_add_library(cudpp ${LIB_TYPE}
  ${CCFILES}
  ${HFILES}
  ${CUHFILES}
  ${HFILES_PUBLIC}
  ${CUFILES}
  OPTIONS ${GENCODE} ${VERBOSE_PTXAS}
  )

install(FILES ${HFILES_PUBLIC}
  DESTINATION include
  )

install(TARGETS cudpp
  DESTINATION lib
  EXPORT cudpp-targets
  )

install(EXPORT cudpp-targets
  DESTINATION lib
  )
