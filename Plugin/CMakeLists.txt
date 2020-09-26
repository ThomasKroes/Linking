cmake_minimum_required(VERSION 3.17)

set(PROJECT "Plugin")

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

add_executable(${PROJECT} ${SOURCES})

target_include_directories(${PROJECT} PRIVATE "${INSTALL_DIR}/$<CONFIGURATION>/include")

if(MSVC)
    set(LIB_LINK_SUFFIX ".lib") 
else()
    set(LIB_LINK_SUFFIX "${CMAKE_SHARED_LIBRARY_SUFFIX}")
endif(MSVC)

target_link_libraries(${PROJECT} "${INSTALL_DIR}/$<CONFIGURATION>/lib/PluginSystem${LIB_LINK_SUFFIX}")

# Determine the file name of the generated export header file
set(EXPORT_HEADER_FILE_NAME ${PROJECT}_export.h)

# Copy the header file after compilation
add_custom_command(TARGET ${PROJECT} POST_BUILD
    COMMAND "${CMAKE_COMMAND}" -E copy_if_different
    "${CMAKE_CURRENT_SOURCE_DIR}/src/${PROJECT}.h"
    "${INSTALL_DIR}/$<CONFIGURATION>/include/${PROJECT}.h"
)

# Copy DLL file after compilation
add_custom_command(TARGET ${PROJECT} POST_BUILD
	COMMAND "${CMAKE_COMMAND}" -E copy_if_different
	"$<TARGET_FILE:${PROJECT}>"
	"${INSTALL_DIR}/$<CONFIGURATION>/$<TARGET_FILE_NAME:${PROJECT}>"
)