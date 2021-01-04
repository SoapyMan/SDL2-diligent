cmake_minimum_required(VERSION 3.6)

project(main)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(DiligentCore)

IF (WIN32)
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


