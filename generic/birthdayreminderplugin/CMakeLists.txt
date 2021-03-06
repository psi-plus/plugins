unset(_HDRS)
unset(_UIS)
unset(_SRCS)
unset(_RSCS)
unset(PLUGIN)

set( PLUGIN birthdayreminderplugin )
project(${PLUGIN} LANGUAGES CXX)
cmake_minimum_required(VERSION 3.1.0)
if(POLICY CMP0071)
    if(${CMAKE_VERSION} VERSION_LESS "3.10.0")
        cmake_policy(SET CMP0071 OLD)
    else()
        cmake_policy(SET CMP0071 NEW)
    endif()
endif()
if(POLICY CMP0074)
    cmake_policy(SET CMP0074 NEW)
endif()
set( CMAKE_AUTOMOC TRUE )

get_filename_component(ABS_PLUGINS_ROOT_DIR "${CMAKE_CURRENT_LIST_DIR}/../.." ABSOLUTE)
set(PLUGINS_ROOT_DIR "${ABS_PLUGINS_ROOT_DIR}" CACHE STRING "Plugins root path. Path where include directory placed")
set( CMAKE_MODULE_PATH
    ${CMAKE_MODULE_PATH}
    ${PLUGINS_ROOT_DIR}/cmake/modules
)
find_package(PsiPluginsApi REQUIRED)
include(${PsiPluginsApi_DIR}/variables.cmake)
include_directories(
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_LIST_DIR}
    ${PsiPluginsApi_INCLUDE_DIR}
)
if(NOT PLUGINS_INSTALL_PATH)
    set(PLUGINS_INSTALL_PATH "${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}")
endif()

set( _SRCS
    ${PLUGIN}.cpp
)
set( _UIS
    options.ui
)
set( _RSCS
    ${PLUGIN}.qrc
)
find_package( Qt5 COMPONENTS Widgets Xml REQUIRED )
set(QT_DEPLIBS
    Qt5::Widgets
    Qt5::Xml
)
qt5_add_resources(RSCS ${_RSCS})
qt5_wrap_ui(UIS ${_UIS})

if(WIN32)
    set(LIB_TYPE "MODULE")
else()
    set(LIB_TYPE "SHARED")
endif()

add_library(
    ${PLUGIN}
    ${LIB_TYPE}
    ${_SRCS}
    ${UIS}
    ${RSCS}
)
target_link_libraries(
    ${PLUGIN}
    ${QT_DEPLIBS}
)

install(
    TARGETS
    ${PLUGIN}
    LIBRARY
    DESTINATION
    ${PLUGINS_INSTALL_PATH}
    RUNTIME DESTINATION
    ${PLUGINS_INSTALL_PATH}
)
