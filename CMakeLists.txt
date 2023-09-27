cmake_minimum_required(VERSION 3.10 FATAL_ERROR)
cmake_policy(VERSION 3.10)

project(CvMatAndQImage VERSION 0.0.1 LANGUAGES CXX)

# A CPP compiler is absolutely needed
set (CMAKE_CXX_STANDARD 14)
set (CMAKE_CXX_STANDARD_REQUIRED ON)

# Enable the compile_commands.json
# See https://cmake.org/cmake/help/latest/variable/CMAKE_EXPORT_COMPILE_COMMANDS.html
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(EXISTS ${CMAKE_SOURCE_DIR}/.git)
    find_package(Git)
endif()

set(QT_COMPONENTS
    Core
    Gui
)
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS ${QT_COMPONENTS})
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS ${QT_COMPONENTS})

SET(OpenCV_MIN_VERSION "4.2.0")
find_package(OpenCV ${OpenCV_MIN_VERSION} REQUIRED)

set(COMMON_INCLUDE_DIRECTORIES
    ${CMAKE_SOURCE_DIR}/lib
    ${OpenCV_INCLUDE_DIRS}
    )
include_directories(${COMMON_INCLUDE_DIRECTORIES})

set(COMMON_LINK_DIRECTORIES
    ${CMAKE_SOURCE_DIR}/bin)
link_directories(${COMMON_LINK_DIRECTORIES})

set(CVMATANDQIMAGE_LIB_TARGET ${PROJECT_NAME})
set(CVMATANDQIMAGE_SOURCES
    ${CMAKE_SOURCE_DIR}/lib/cvmatandqimage.cpp
    )

add_library(${CVMATANDQIMAGE_LIB_TARGET} STATIC ${CVMATANDQIMAGE_SOURCES})

set(TARGET_LINK_LIBRARIES_PRIVATE
    PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::Gui
    ${OpenCV_LIBS}
)
if (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    list(APPEND TARGET_LINK_LIBRARIES_PRIVATE
                ws2_32)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    list(APPEND TARGET_LINK_LIBRARIES_PRIVATE
                pthread)
endif ()

target_link_libraries(
    ${CVMATANDQIMAGE_LIB_TARGET}
    PRIVATE
        ${TARGET_LINK_LIBRARIES_PRIVATE})

target_include_directories(
    ${CVMATANDQIMAGE_LIB_TARGET}
    PUBLIC
        ${COMMON_INCLUDE_DIRECTORIES})
