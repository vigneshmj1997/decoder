cmake_minimum_required(VERSION 3.5.1)

project(wav2letter++)

# ----------------------------- Setup -----------------------------
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ----------------------------- Configuration -----------------------------

option(W2L_BUILD_LIBRARIES_ONLY "Build wav2letter-libraries only" OFF)
option(W2L_BUILD_INFERENCE
  "Build inference even when W2L_BUILD_LIBRARIES_ONLY is set" OFF)
option(W2L_LIBRARIES_USE_CUDA "Use CUDA in libraries-only build" ON)
option(W2L_LIBRARIES_USE_KENLM "Use KenLM in libraries-only build" ON)
option(W2L_LIBRARIES_USE_MKL "Use MKL in libraries-only build" ON)
option(W2L_BUILD_FOR_PYTHON "Build Python bindings" ON)
option(W2L_BUILD_TESTS "Build tests for wav2letter++" OFF)
option(W2L_BUILD_EXAMPLES "Build examples for wav2letter++" ON)
option(W2L_BUILD_EXPERIMENTAL "Build internal experimental components for wav2letter++" OFF)
option(W2L_BUILD_SCRIPTS "Build internal scripts for wav2letter++" OFF)
option(W2L_BUILD_RECIPES "Build recipes" OFF)
option(W2L_BUILD_TOOLS "Build audio tools" OFF)
set(KENLM_MAX_ORDER 6 CACHE STRING "Maximum ngram order for KenLM")

# ------------------------- Dependency Fallback -------------------------

if (W2L_LIBRARIES_USE_MKL)
  find_package(MKL)
  if (NOT MKL_FOUND)
    message(WARNING "MKL not found; forcing W2L_LIBRARIES_USE_MKL=OFF.")
    set(W2L_LIBRARIES_USE_MKL OFF CACHE BOOL "" FORCE)
  endif ()
endif ()

# ------------------------- Libraries-Only Build ------------------------------

function (build_w2l_libraries)
  set(W2L_COMPILE_DEFINITIONS
    $<$<BOOL:${W2L_LIBRARIES_USE_CUDA}>:W2L_LIBRARIES_USE_CUDA>
    $<$<BOOL:${W2L_LIBRARIES_USE_KENLM}>:W2L_LIBRARIES_USE_KENLM>
    $<$<BOOL:${W2L_LIBRARIES_USE_MKL}>:W2L_LIBRARIES_USE_MKL>
    )
  add_subdirectory(${PROJECT_SOURCE_DIR}/src/libraries)
  if (W2L_BUILD_FOR_PYTHON)
    add_subdirectory(${PROJECT_SOURCE_DIR}/bindings/python)
  endif ()
endfunction ()


# Find and setup OpenMP
find_package(OpenMP)
if (OPENMP_FOUND)
  message(STATUS "OpenMP found")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  set(CMAKE_EXE_LINKER_FLAGS
    "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}"
  )
else()
  message(STATUS "OpenMP not found - building without OpenMP")
endif()


set(W2L_LIBRARIES_USE_KENLM ON CACHE BOOL "" FORCE)




if (W2L_BUILD_TESTS)
  enable_testing()
  add_subdirectory(${PROJECT_SOURCE_DIR}/src/tests)
endif ()


