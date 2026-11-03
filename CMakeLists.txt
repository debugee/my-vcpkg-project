cmake_minimum_required(VERSION 3.30)

project(test VERSION 0.1.0 LANGUAGES C CXX)

if (MINGW)
    set(VCPKG_TARGET_TRIPLET "x64-mingw-dynamic")
elseif (APPLE)
    set(VCPKG_TARGET_TRIPLET "x64-osx")
else()
    message(FATAL_ERROR "Unsupported platform: ${CMAKE_SYSTEM_NAME}")
endif()

if(DEFINED ENV{VCPKG_ROOT})
    file(TO_CMAKE_PATH "$ENV{VCPKG_ROOT}" CMAKE_VCPKG_ROOT)
    include("${CMAKE_VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake")
else()
    message(FATAL_ERROR "Environment variable VCPKG_ROOT is not set")
endif()

add_executable(test main.cpp)

find_package(MyLibrary CONFIG REQUIRED)

target_link_libraries(test PRIVATE MyLibrary::MyLibrary)

target_compile_options(test PRIVATE -v)
target_link_options(test PRIVATE -v)
