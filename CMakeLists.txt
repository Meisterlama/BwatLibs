cmake_minimum_required(VERSION 3.17)
set(CMAKE_CXX_STANDARD 17)

project(BwatLibs LANGUAGES CXX C)

option(BWAT_ENGINE_TEST_ENABLED "Whether or not we are running the test of BwatEngine" OFF)

# ASSIMP BUILD
set(ASSIMP_BUILD_ALL_IMPORTERS_BY_DEFAULT OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_GLTF_IMPORTER ON CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_FBX_IMPORTER ON CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_OBJ_IMPORTER ON CACHE BOOL "" FORCE)
set(ASSIMP_NO_EXPORT ON CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ASSIMP_INSTALL OFF CACHE BOOL "" FORCE)

add_subdirectory(assimp)

# FLATBUFFERS BUILD
set(FLATBUFFERS_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(FLATBUFFERS_STATIC_FLATC ON CACHE BOOL "" FORCE)
set(FLATBUFFERS_INSTALL OFF CACHE BOOL "" FORCE)

add_subdirectory(flatbuffers)

# GLAD BUILD
include_directories(PRIVATE gladlib)
set(GLAD_SRC
        gladlib/glad/glad.c)

add_library(glad STATIC ${GLAD_SRC})
target_include_directories(glad PUBLIC gladlib)

# GLFW BUILD
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)
set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)

add_subdirectory(glfw)
#For some reasons the next line breaks everything
#target_compile_definitions(glfw PRIVATE -DGLFW_INCLUDE_NONE)

# GTEST AND GLM BUILD (TEST ONLY)
if (BWAT_ENGINE_TEST_ENABLED)
    include_directories(PRIVATE googletest/googletest/include)

    set(INSTALL_GTEST OFF CACHE BOOL "" FORCE)

    add_subdirectory(googletest)
    add_subdirectory(glm)
endif()

# IMGUI BUILD
file(GLOB_RECURSE IMGUI_SRC
        imgui/src/*.cpp)

add_library(imgui STATIC ${IMGUI_SRC})
target_include_directories(imgui PUBLIC imgui/include)
target_link_libraries(imgui PUBLIC glfw glad)

# OPENAL_SOFT BUILD
set(ALSOFT_UTILS OFF CACHE BOOL "" FORCE)
set(ALSOFT_EXAMPLES OFF CACHE BOOL "" FORCE)
set(ALSOFT_INSTALL  OFF CACHE BOOL "" FORCE)
set(ALSOFT_INSTALL_CONFIG  OFF CACHE BOOL "" FORCE)
set(ALSOFT_INSTALL_HRTF_DATA  OFF CACHE BOOL "" FORCE)
set(ALSOFT_INSTALL_AMBDEC_PRESETS  OFF CACHE BOOL "" FORCE)
set(ALSOFT_INSTALL_EXAMPLES  OFF CACHE BOOL "" FORCE)
set(ALSOFT_INSTALL_UTILS  OFF CACHE BOOL "" FORCE)
set(ALSOFT_UPDATE_BUILD_VERSION  OFF CACHE BOOL "" FORCE)

add_subdirectory(openal-soft)

# PHYSX BUILD
set(PHYSX_ROOT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/PhysX/physx)
set(PHYSX_INCLUDE_DIR ${PHYSX_ROOT_DIR}/include/ ${PHYSX_ROOT_DIR}/../pxshared/include/)
set(PHYSX_LIBRARIES
        PhysXExtensions
        PhysX
        PhysXPvdSDK
        PhysXVehicle
        PhysXCharacterKinematic
        PhysXCooking
        PhysXCommon
        PhysXFoundation
        )

if (WIN32)
    set(TARGET_BUILD_PLATFORM "windows")
    set(NV_USE_STATIC_WINCRT ON CACHE BOOL "Use the statically linked windows CRT")
    set(NV_USE_DEBUG_WINCRT ON CACHE BOOL "Use the debug version of the CRT")
else()
    set(CMAKE_LIBRARY_ARCHITECTURE x86_64-linux-gnu)
    set(TARGET_BUILD_PLATFORM "linux")
endif ()
set(PX_BUILDSNIPPETS OFF CACHE BOOL "Generate the snippets")
set(PX_BUILDPUBLICSAMPLES OFF CACHE BOOL "Generate the samples projects")
set(PX_GENERATE_STATIC_LIBRARIES ON CACHE BOOL "Generate static libraries")
set(PX_FLOAT_POINT_PRECISE_MATH OFF CACHE BOOL "Float point precise math")
set(PXSHARED_PATH ${PHYSX_ROOT_DIR}/../pxshared)
set(PXSHARED_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX})
set(CMAKEMODULES_VERSION "1.27")
set(CMAKEMODULES_PATH ${PHYSX_ROOT_DIR}/../externals/cmakemodules)
set(PX_OUTPUT_LIB_DIR ${CMAKE_CURRENT_BINARY_DIR}/externals/physx)
set(PX_OUTPUT_BIN_DIR ${CMAKE_CURRENT_BINARY_DIR}/externals/physx)

add_subdirectory(${PHYSX_ROOT_DIR}/compiler/public externals/physx)
include_directories(PUBLIC ${PHYSX_ROOT_DIR}/include)

# STB BUILD
set(STB_SRC
        stb/src/stb_image.cpp
        stb/src/stb_perlin.cpp
        )

add_library(stb STATIC ${STB_SRC})
target_include_directories(stb PUBLIC stb/include)

# JSON BUILD
set(JSON_SRC
		json/include/json.hpp)

add_library(json INTERFACE)
target_include_directories(json INTERFACE json/include)


# DIRENT BUILD
add_library(dirent INTERFACE)
target_include_directories(dirent INTERFACE dirent/include)

add_library(AudioFile INTERFACE)
target_include_directories(AudioFile INTERFACE AudioFile/include)

# LUA BUILD
add_subdirectory(lua)
