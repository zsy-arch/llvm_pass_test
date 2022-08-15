cmake_minimum_required(VERSION 3.23)
project(llvm_test1)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_FLAGS_RELEASE "/MTd")
set(CMAKE_CXX_FLAGS_DEBUG "/MTd")

message("llvm home at: $ENV{LLVM_HOME}")
set(ENV{LLVM_DIR} $ENV{LLVM_HOME}/lib/cmake/llvm)
find_package(LLVM REQUIRED CONFIG)
add_definitions(${LLVM_DEFINITIONS})
include_directories(${LLVM_INCLUDE_DIRS})
link_directories(${LLVM_LIBRARY_DIRS})
add_compile_options(-std=c++14)

add_library(llvm_test1 SHARED library.cpp)
target_link_libraries(
        llvm_test1
        LLVMCore
        LLVMSupport
        LLVMipo
)
# LLVM is (typically) built with no C++ RTTI. We need to match that;
# otherwise, we'll get linker errors about missing RTTI data.
set_target_properties(llvm_test1 PROPERTIES
        COMPILE_FLAGS "-fno-rtti"
        )
# Get proper shared-library behavior (where symbols are not necessarily
# resolved when the shared library is linked) on OS X.
if (APPLE)
    set_target_properties(llvm_test1 PROPERTIES
            LINK_FLAGS "-undefined dynamic_lookup"
            )
endif (APPLE)