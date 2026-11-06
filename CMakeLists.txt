cmake_minimum_required(VERSION 3.30)

project(test VERSION 0.1.0 LANGUAGES C CXX)

if (MINGW)
    set(VCPKG_TARGET_TRIPLET "x64-mingw-dynamic")
elseif (APPLE)
    set(VCPKG_TARGET_TRIPLET "x64-osx")
else()
    message(FATAL_ERROR "Unsupported platform: ${CMAKE_SYSTEM_NAME}")
endif()


#debug mylib’s CMakeLists.txt, if you want to debug mylib’s CMakeLists.txt uncomment the following two line
#set VCPKG_BUILD_TYPE release is very important, if not, Ninja debug and release compile run at a the same time,and pipe(same pipe name) error will occur
#set(VCPKG_OVERLAY_TRIPLETS "${CMAKE_CURRENT_SOURCE_DIR}/overlays/triplets")
#set(VCPKG_INSTALL_OPTIONS "--binarysource=clear;--x-cmake-configure-debug=/tmp/vcpkg_configure_pipe\;mylib")


#debug mylib’s portfile.cmake
#mylib in overlays/ports/mylib is a copy of mylib for debugging purposes, when debug ok, you can commit it to git my-vcpkg-registry.git
#and then run vcpkg x-add-version --all  --vcpkg-root=. in my-vcpkg-registry.git to update the versions/ and baseline.json
#and then commit and push the new versions/ and baseline.json to my-vcpkg-registry.git
#and then update vcpkg-configuration.json baseline to the new commit id of my-vcpkg-registry.git
#if you want to debug mylib’s portfile.cmake uncomment the following two line
#set(VCPKG_OVERLAY_PORTS "${CMAKE_CURRENT_SOURCE_DIR}/overlays/ports")
#set(VCPKG_INSTALL_OPTIONS "--binarysource=clear;--x-cmake-debug=/tmp/vcpkg_configure_pipe\;mylib")

if(DEFINED ENV{VCPKG_ROOT})
    file(TO_CMAKE_PATH "$ENV{VCPKG_ROOT}" CMAKE_VCPKG_ROOT)
    include("${CMAKE_VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake")
else()
    message(FATAL_ERROR "Environment variable VCPKG_ROOT is not set")
endif()

add_executable(test main.cpp)


set(USE_PKG_CONFIG ON)

if (USE_PKG_CONFIG)
    message(STATUS "Using pkg-config to find MyLibrary")
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(MyLibrary REQUIRED IMPORTED_TARGET mylibrary)
    # target_include_directories(test PRIVATE ${MyLibrary_INCLUDE_DIRS})
    # target_link_directories(test PRIVATE ${MyLibrary_LIBRARY_DIRS})
    # target_link_libraries(test PRIVATE ${MyLibrary_LIBRARIES})
    target_link_libraries(test PRIVATE PkgConfig::MyLibrary)
else()
    message(STATUS "Using CMake find_package to find MyLibrary")
    find_package(MyLibrary REQUIRED)
    target_link_libraries(test PRIVATE MyLibrary::MyLibrary)
endif()