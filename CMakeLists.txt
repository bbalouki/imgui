cmake_minimum_required(VERSION 3.20)
project(ImGui VERSION "1.92.5" LANGUAGES CXX)

include(GNUInstallDirs)
# Library Target
add_library(imgui
    imgui.cpp
    imgui_draw.cpp
    imgui_tables.cpp
    imgui_widgets.cpp
    imgui_demo.cpp
    backends/imgui_impl_glfw.cpp
    backends/imgui_impl_opengl3.cpp
    misc/cpp/imgui_stdlib.cpp
)
add_library(ImGui::ImGui ALIAS imgui)
find_package(glfw3 CONFIG REQUIRED)
find_package(OpenGL REQUIRED)
target_link_libraries(imgui PUBLIC glfw OpenGL::GL)

target_include_directories(imgui PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/backends>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/misc/cpp>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/imgui>
)
# 1. Install the main ImGui header files
file(GLOB IMGUI_ROOT_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/*.h")
install(FILES ${IMGUI_ROOT_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/imgui)

# 2. Install the backend header files into their own subdirectory
set(IMGUI_BACKEND_HEADERS
    "${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.h"
)
install(FILES ${IMGUI_BACKEND_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/imgui/backends)

# 3. Install the misc header files into their own subdirectory
set(IMGUI_MISC_HEADERS
    "${CMAKE_CURRENT_SOURCE_DIR}/misc/cpp/imgui_stdlib.h"
)
install(FILES ${IMGUI_MISC_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/imgui/misc)

# 4. Install the compiled library targets and create the CMake export set
install(TARGETS imgui
    EXPORT ImGuiTargets
    # This installs both the release and debug versions into the same directories
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

# 5. Generate and install the ImGuiTargets.cmake file
install(EXPORT ImGuiTargets
    FILE ImGuiTargets.cmake
    NAMESPACE ImGui::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ImGui
)

# 6. Generate and install the ImGuiConfig.cmake file
include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${CMAKE_CURRENT_LIST_DIR}/cmake/ImGuiConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/ImGuiConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ImGui
)

write_basic_package_version_file(
    "ImGuiConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)
install(
    FILES 
    "${CMAKE_CURRENT_BINARY_DIR}/ImGuiConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/ImGuiConfigVersion.cmake"
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/ImGui"
)

