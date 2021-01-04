cmake_minimum_required(VERSION 3.6)

project(main CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(DiligentCore)

IF (PLATFORM_WIN32)
	if(NOT SDL2_DIR)
	  set(SDL2_DIR "" CACHE PATH "SDL2 directory")
	endif()

	set(SDL2_INCLUDE_DIRS "${SDL2_DIR}/include")

	if (${CMAKE_SIZEOF_VOID_P} MATCHES 8)
	  set(SDL2_LIBRARIES "${SDL2_DIR}/lib/x64/SDL2.lib;${SDL2_DIR}/lib/x64/SDL2main.lib")
	else ()
	  set(SDL2_LIBRARIES "${SDL2_DIR}/lib/x86/SDL2.lib;${SDL2_DIR}/lib/x86/SDL2main.lib")
	endif ()

	string(STRIP "${SDL2_LIBRARIES}" SDL2_LIBRARIES)
ELSE()
	find_package(SDL2 REQUIRED)
ENDIF()

include_directories(${SDL2_INCLUDE_DIRS})
include_directories(DiligentCore)

set(SRC main.cpp)

add_executable(main ${SRC})
target_link_libraries(main
  ${SDL2_LIBRARIES}
  Diligent-GraphicsEngine
)

target_compile_definitions(main 
PRIVATE 
    UNICODE
    D3D11_SUPPORTED=$<BOOL:${D3D11_SUPPORTED}>
    D3D12_SUPPORTED=$<BOOL:${D3D12_SUPPORTED}>
    GL_SUPPORTED=$<BOOL:${GL_SUPPORTED}>
    VULKAN_SUPPORTED=$<BOOL:${VULKAN_SUPPORTED}>
	PLATFORM_WIN32=$<BOOL:${PLATFORM_WIN32}>
	PLATFORM_UNIVERSAL_WINDOWS=$<BOOL:${PLATFORM_UNIVERSAL_WINDOWS}>
	PLATFORM_ANDROID=$<BOOL:${PLATFORM_ANDROID}>
	PLATFORM_LINUX=$<BOOL:${PLATFORM_LINUX}>
	PLATFORM_MACOS=$<BOOL:${PLATFORM_MACOS}>
	PLATFORM_IOS=$<BOOL:${PLATFORM_IOS}>
)

target_compile_definitions(main PRIVATE ENGINE_DLL=0)

if (D3D11_SUPPORTED)
  target_link_libraries(main Diligent-GraphicsEngineD3D11-static)
endif()

if (D3D12_SUPPORTED)
  target_link_libraries(main Diligent-GraphicsEngineD3D12-static)
endif()

if (GL_SUPPORTED)
  target_link_libraries(main Diligent-GraphicsEngineOpenGL-static)
endif()

if (VULKAN_SUPPORTED)
  target_link_libraries(main Diligent-GraphicsEngineVk-static)
endif()

if (METAL_SUPPORTED)
  target_link_libraries(main Diligent-GraphicsEngineMetal-static)
endif()


