cmake_minimum_required(VERSION 3.17)

set(PROJECT "PluginSystem")

PROJECT(${PROJECT})

set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W3 /DWIN32 /EHsc /MP")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /NODEFAULTLIB:LIBCMT")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MDd")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MD")
endif(MSVC)

file(GLOB SOURCES src/*.h src/*.cpp)

include_directories("../$<CONFIGURATION>/include/")

add_library(${PROJECT} SHARED ${SOURCES})

target_link_libraries(${PROJECT} LINK_PUBLIC ThirdParty)

add_dependencies(${PROJECT} ThirdParty)

# Generate an export header file for this project
include(GenerateExportHeader)
generate_export_header(${PROJECT})

# Determine the file name of the generated export header file
set(EXPORT_HEADER_FILE_NAME ${PROJECT}_export.h)

# Copy the header file after compilation
add_custom_command(TARGET ${PROJECT} POST_BUILD
    COMMAND "${CMAKE_COMMAND}" -E copy_if_different
    "${CMAKE_CURRENT_SOURCE_DIR}/src/${PROJECT}.h"
    "${INSTALL_DIR}/$<CONFIGURATION>/include/${PROJECT}.h"
)

# Copy the export header file after compilation
add_custom_command(TARGET ${PROJECT} POST_BUILD
    COMMAND "${CMAKE_COMMAND}" -E copy_if_different
    "${CMAKE_CURRENT_BINARY_DIR}/${EXPORT_HEADER_FILE_NAME}"
    "${INSTALL_DIR}/$<CONFIGURATION>/include/${EXPORT_HEADER_FILE_NAME}"
)

# Copy DLL file after compilation
add_custom_command(TARGET ${PROJECT} POST_BUILD
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different
	"$<TARGET_FILE:${PROJECT}>"
	"${INSTALL_DIR}/$<CONFIGURATION>/$<TARGET_FILE_NAME:${PROJECT}>"
)

# Copy LIB file after compilation
add_custom_command(TARGET ${PROJECT} POST_BUILD
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different
	"${CMAKE_CURRENT_BINARY_DIR}/$<CONFIGURATION>/${PROJECT}.lib"
	"${INSTALL_DIR}/$<CONFIGURATION>/lib/${PROJECT}.lib"
)