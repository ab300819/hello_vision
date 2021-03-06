cmake_minimum_required(VERSION 3.5.1)
project(hello_vision LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

message(STATUS ${CMAKE_SYSTEM_NAME})
if (CMAKE_SYSTEM_NAME MATCHES "Windows")
    #    set(OpenCV_DIR )
elseif (CMAKE_SYSTEM_NAME MATCHES "Darwin")
    set(OpenCV_DIR /Users/mason/Project/source/opencv4/lib/cmake/opencv4)
    set(VTK_DIR /Users/mason/Project/source/vtk/lib/cmake/vtk-8.2)

    set(TBB_DIR /Users/mason/Project/source/tbb/cmake)
    set(TBB_INCLUDE_DIR /Users/mason/Project/source/tbb/include)
    set(TBB_LIBRARY_DEBUG /Users/mason/Project/source/tbb/lib)
    set(TBB_LIBRARY_RELEASE /Users/mason/Project/source/tbb/lib)

    set(Qt5_DIR /usr/local/Cellar/qt/5.14.0/lib/cmake/Qt5)
elseif (CMAKE_SYSTEM_NAME MATCHES "Linux")
    #    set(OpenCV_DIR )
endif ()

# recommend use CMAKE_PREFIX_PATH instead
find_package(OpenCV REQUIRED)
find_package(VTK REQUIRED)
find_package(TBB REQUIRED)
find_package(Qt5 COMPONENTS Widgets REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})
add_executable(opencv source/opencv/opencv.cpp)
target_link_libraries(opencv ${OpenCV_LIBS})

find_package(VTK COMPONENTS
        vtkCommonCore
        vtkCommonDataModel QUIET)
if (NOT VTK_FOUND)
    message("Skipping DistancePointToLine: ${VTK_NOT_FOUND_MESSAGE}")
    return()
endif ()
message(STATUS "VTK_VERSION: ${VTK_VERSION}")
if (VTK_VERSION VERSION_LESS "8.90.0")
    # old system
    include(${VTK_USE_FILE})
    add_executable(DistancePointToLine MACOSX_BUNDLE DistancePointToLine.cxx)
    target_link_libraries(DistancePointToLine PRIVATE ${VTK_LIBRARIES})
else ()
    # include all components
    add_executable(DistancePointToLine MACOSX_BUNDLE DistancePointToLine.cxx)
    target_link_libraries(DistancePointToLine PRIVATE ${VTK_LIBRARIES})
    # vtk_module_autoinit is needed
    vtk_module_autoinit(
            TARGETS DistancePointToLine
            MODULES ${VTK_LIBRARIES}
    )
endif ()
add_executable(vtk source/vtk/vtk.cpp)
target_link_libraries(vtk ${VTK_LIBRARIES})

add_executable(main
        source/main.cpp
        source/widget/widget.cpp
        source/widget/widget.h
        source/widget/widget.ui)
target_link_libraries(main Qt5::Widgets)
