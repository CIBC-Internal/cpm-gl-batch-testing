cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME gl_batch_testing)
set(CPM_LIB_TARGET_NAME ${CPM_MODULE_NAME})

if ((DEFINED CPM_DIR) AND (DEFINED CPM_UNIQUE_ID) AND (DEFINED CPM_TARGET_NAME))
  set(CPM_LIB_TARGET_NAME ${CPM_TARGET_NAME})
  set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CPM_DIR})
  include(CPM)
else()
  set(CPM_DIR "${CMAKE_CURRENT_BINARY_DIR}/cpm-packages" CACHE TYPE STRING)
  find_package(Git)
  if(NOT GIT_FOUND)
    message(FATAL_ERROR "CPM requires Git.")
  endif()
  if (NOT EXISTS ${CPM_DIR}/CPM.cmake)
    execute_process(
      COMMAND "${GIT_EXECUTABLE}" clone https://github.com/CIBC-Internal/cpm ${CPM_DIR}
      RESULT_VARIABLE error_code
      OUTPUT_VARIABLE head_sha)
    if(error_code)
      message(FATAL_ERROR "CPM failed to get the hash for HEAD")
    endif()
  endif()
  include(${CPM_DIR}/CPM.cmake)
endif()

# All externals *must* define this.
CPM_ForceOnlyOneModuleVersion()

# ++ EXTERNAL-MODULE: Google Test
CPM_AddModule("google_test"
  GIT_REPOSITORY "https://github.com/CIBC-Internal/cpm-google-test"
  GIT_TAG "origin/master"
  EXPORT_MODULE TRUE
  USE_EXISTING_VER TRUE)

# ++ MODULE: Spire
CPM_AddModule("spire"
  GIT_REPOSITORY "https://github.com/CIBC-Internal/spire"
  GIT_TAG "origin/master"
  EXPORT_MODULE TRUE
  USE_EXISTING_VER TRUE)

# ++ MODULE: GL Platform
CPM_AddModule("gl_platform"
  GIT_REPOSITORY "https://github.com/CIBC-Internal/cpm-gl-platform.git"
  GIT_TAG "origin/master"
  EXPORT_MODULE TRUE
  USE_EXISTING_VER TRUE)

# ++ MODULE: GL Batch Context
CPM_AddModule("gl_batch_context"
  GIT_REPOSITORY "https://github.com/CIBC-Internal/cpm-gl-batch-context.git"
  GIT_TAG "origin/master")

# ++ MODULE: GL State
CPM_AddModule("gl_state"
  GIT_REPOSITORY "https://github.com/CIBC-Internal/cpm-gl-state.git"
  GIT_TAG "origin/master")

# ++ EXTERNAL-MODULE: Lodepng
CPM_AddModule("lodepng"
  GIT_REPOSITORY "https://github.com/CIBC-Internal/cpm-lodepng.git"
  GIT_TAG "origin/master")

CPM_InitModule(${CPM_MODULE_NAME})

#-----------------------------------------------------------------------
# Source
#-----------------------------------------------------------------------

# Globbing has some downsides, but the advantages outweigh the
# disadvantages.
file (GLOB Sources
  "batch-testing/*.cpp"
  "batch-testing/*.h"
  )

#-----------------------------------------------------------------------
# Library setup
#-----------------------------------------------------------------------

# Build the library.
add_library(${CPM_LIB_TARGET_NAME} ${Sources})
target_link_libraries(${CPM_LIB_TARGET_NAME} 
  ${OS_SPECIFIC_LIBS} 
  ${CPM_LIBRARIES})

