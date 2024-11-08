# Note: CMake support is community-based. The maintainers do not use CMake
# internally.

cmake_minimum_required(VERSION 3.13)

project(googletest-distribution)
set(GOOGLETEST_VERSION 1.15.2)

if(NOT CYGWIN AND NOT MSYS AND NOT ${CMAKE_SYSTEM_NAME} STREQUAL QNX)
  set(CMAKE_CXX_EXTENSIONS OFF)
endif()

enable_testing()

include(CMakeDependentOption)
include(GNUInstallDirs)

# Note that googlemock target already builds googletest.
option(BUILD_GMOCK "Builds the googlemock subproject" ON)
option(INSTALL_GTEST "Enable installation of googletest. (Projects embedding googletest may want to turn this OFF.)" OFF)
option(GTEST_HAS_ABSL "Use Abseil and RE2. Requires Abseil and RE2 to be separately added to the build." OFF)
option(CUSTOM_INSTALL "Custom Install" ON)

if(GTEST_HAS_ABSL)
  if(NOT TARGET absl::base)
    find_package(absl REQUIRED)
  endif()
  if(NOT TARGET re2::re2)
    find_package(re2 REQUIRED)
  endif()
endif()

if(BUILD_GMOCK)
  add_subdirectory( googlemock )
else()
  add_subdirectory( googletest )
endif()


#######Custom Install########
if (CUSTOM_INSTALL)
  install(TARGETS gtest gtest_main gmock gmock_main
          ARCHIVE DESTINATION ${TEST_INSTALL_PREFIX}/googletest/lib
          LIBRARY DESTINATION ${TEST_INSTALL_PREFIX}/googletest/lib
          RUNTIME DESTINATION ${TEST_INSTALL_PREFIX}/googletest/bin)

  install(DIRECTORY
          googletest/include googlemock/include
          DESTINATION ${TEST_INSTALL_PREFIX}/googletest/
          FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")
endif()