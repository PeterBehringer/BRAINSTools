cmake_minimum_required(VERSION 2.8.7)

set(LOCAL_PROJECT_NAME BRAINSTools)
set(PRIMARY_PROJECT_NAME ${LOCAL_PROJECT_NAME})

## NOTE THERE SHOULD BE NO PROJECT STATEMENT HERE!
## This file acts as a simple switch to initiate
## two completely independant CMake build environments.

#-----------------------------------------------------------------------------
# Superbuild Option - Enabled by default
#                   Phase I:  ${LOCAL_PROJECT_NAME}_SUPERBUILD is set to ON, and the
#                             supporting packages defined in "SuperBuild.cmake"
#                             are built.  The last package in "SuperBuild.cmake"
#                             to be built is a recursive call to this
#                             file with ${LOCAL_PROJECT_NAME}_SUPERBUILD explicitly
#                             set to "OFF" to initiate Phase II
#
#                   Phase II: Build the ${LOCAL_PROJECT_NAME}, referencing the support
#                             packages built in Phase I.
#-----------------------------------------------------------------------------
option(${LOCAL_PROJECT_NAME}_SUPERBUILD "Build ${LOCAL_PROJECT_NAME} and the projects it depends on via SuperBuild.cmake." ON)
mark_as_advanced(${LOCAL_PROJECT_NAME}_SUPERBUILD)

if(${LOCAL_PROJECT_NAME}_SUPERBUILD)
  project(SuperBuild_${LOCAL_PROJECT_NAME} C CXX)
else()
  project(${LOCAL_PROJECT_NAME} C CXX)
endif()

enable_language(C)
enable_language(CXX)

#-----------------------------------------------------------------------------
# Common build features for both the superbuild and the main build
#-----------------------------------------------------------------------------
include(${CMAKE_CURRENT_SOURCE_DIR}/Common.cmake)

#-----------------------------------------------------------------------------
# Sanity checks
#------------------------------------------------------------------------------
include(PreventInSourceBuilds)
include(PreventInBuildInstalls)
include(itkCheckSourceTree)

#-----------------------------------------------------------------------------
# Superbuild script
#-----------------------------------------------------------------------------
if(${LOCAL_PROJECT_NAME}_SUPERBUILD)
  project(SuperBuild_${LOCAL_PROJECT_NAME} C CXX)
  include("${CMAKE_CURRENT_SOURCE_DIR}/SuperBuild.cmake")
  return()
else()
  include("${CMAKE_CURRENT_SOURCE_DIR}/${LOCAL_PROJECT_NAME}.cmake")
  return()
endif()

message(FATAL_ERROR "You should never reach this point !")

