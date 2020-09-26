# CMakeLists to generate a project that has all subprojects of the plugin system in one.

cmake_minimum_required(VERSION 3.17)

set(PROJECT "Linking")

PROJECT(${PROJECT})

set(INSTALL_DIR CACHE PATH "Install dir of the linking project")

add_subdirectory(ThirdParty)
add_subdirectory(PluginSystem)
add_subdirectory(Plugin)