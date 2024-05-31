cmake_minimum_required(VERSION 3.28)
project(Hello42)

set(CMAKE_CXX_STANDARD 20)

file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS ./src/*)
add_executable(${PROJECT_NAME} ${SOURCE_FILES})
target_include_directories(${PROJECT_NAME} PRIVATE src)
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_20)

# GLFW #
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)
add_subdirectory(./external/glfw)
target_link_libraries(${PROJECT_NAME} PRIVATE glfw)

# Vulkan #
# find_package(Vulkan REQUIRED)
# target_include_directories(${PROJECT_NAME} PRIVATE ${Vulkan_INCLUDE_DIRS})
# target_link_libraries(${PROJECT_NAME} PRIVATE ${Vulkan_LIBRARIES})
add_subdirectory(./external/vulkan-header)
target_link_libraries(${PROJECT_NAME} PRIVATE Vulkan-Headers)

# ImGUI #
add_library(ImGui)
target_compile_features(ImGui PUBLIC cxx_std_11)

if (WIN32)
    target_link_libraries(ImGui PRIVATE imm32) # Required only when using mingw, not Visual Studio
endif()

target_include_directories(ImGui PUBLIC ./external/imgui)
target_sources(ImGui PRIVATE
        ./external/imgui/imgui_demo.cpp
        ./external/imgui/imgui_draw.cpp
        ./external/imgui/imgui_tables.cpp
        ./external/imgui/imgui_widgets.cpp
        ./external/imgui/imgui.cpp
        ./external/imgui/misc/cpp/imgui_stdlib.cpp
        ./external/imgui/backends/imgui_impl_vulkan.cpp
        ./external/imgui/backends/imgui_impl_glfw.cpp
)

target_link_libraries(ImGui PRIVATE Vulkan-Headers)
target_link_libraries(ImGui PRIVATE glfw)
target_link_libraries(${PROJECT_NAME} PRIVATE ImGui)

# NVRHI #
set(NVRHI_BUILD_SHARED ON CACHE BOOL "" FORCE)
set(NVRHI_INSTALL ON CACHE BOOL "" FORCE)
set(NVRHI_INSTALL_EXPORTS OFF CACHE BOOL "" FORCE)
set(NVRHI_WITH_VALIDATION ON CACHE BOOL "" FORCE)
set(NVRHI_WITH_VULKAN ON CACHE BOOL "" FORCE)
set(NVRHI_WITH_RTXMU ON CACHE BOOL "" FORCE)
set(NVRHI_WITH_NVAPI OFF CACHE BOOL "" FORCE)
set(NVRHI_WITH_DX11 OFF CACHE BOOL "" FORCE)
set(NVRHI_WITH_DX12 OFF CACHE BOOL "" FORCE)

add_subdirectory(./external/nvrhi)
target_link_libraries(${PROJECT_NAME} ${nvrhi_vulkan_target})
target_link_libraries(${PROJECT_NAME} PRIVATE nvrhi)

