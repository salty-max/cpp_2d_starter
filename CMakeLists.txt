cmake_minimum_required(VERSION 3.28)
project(cpp_2d_starter LANGUAGES CXX)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)


include(FetchContent)

message(STATUS "Fetching SFML...")

FetchContent_Declare(SFML
    GIT_REPOSITORY https://github.com/SFML/SFML.git
    GIT_TAG 2.6.x
    GIT_SHALLOW ON
    EXCLUDE_FROM_ALL
    SYSTEM)
FetchContent_MakeAvailable(SFML)

message(STATUS "Fetching ImGui...")

FetchContent_Declare(imgui
    GIT_REPOSITORY https://github.com/ocornut/imgui.git
    GIT_TAG v1.88
    GIT_SHALLOW ON
    EXCLUDE_FROM_ALL
    SYSTEM)
FetchContent_MakeAvailable(imgui)

message(STATUS "Fetching ImGui-SFML...")

FetchContent_Declare(imgui-sfml
    GIT_REPOSITORY https://github.com/SFML/imgui-sfml.git
    GIT_TAG v2.6  # Match SFML version if possible
    GIT_SHALLOW ON
    EXCLUDE_FROM_ALL
    SYSTEM)
    
# Set IMGUI_DIR explicitly
set(IMGUI_DIR ${imgui_SOURCE_DIR})
set(IMGUI_SFML_FIND_SFML OFF) # Ensure it uses the fetched SFML
set(IMGUI_SFML_IMGUI_DEMO ON)
FetchContent_MakeAvailable(imgui-sfml)

add_executable(cpp_2d_starter src/main.cpp)
target_link_libraries(cpp_2d_starter PRIVATE sfml-graphics sfml-window sfml-system sfml-audio sfml-network ImGui-SFML::ImGui-SFML)
target_compile_features(cpp_2d_starter PRIVATE cxx_std_17)

if(WIN32)
    add_custom_command(
        TARGET cpp_2d_starter
        COMMENT "Copy OpenAL DLL"
        PRE_BUILD COMMAND ${CMAKE_COMMAND} -E copy ${SFML_SOURCE_DIR}/extlibs/bin/$<IF:$<EQUAL:${CMAKE_SIZEOF_VOID_P},8>,x64,x86>/openal32.dll $<TARGET_FILE_DIR:cpp_2d_starter>
        VERBATIM)
endif()