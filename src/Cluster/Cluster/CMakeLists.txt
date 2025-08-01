cmake_minimum_required(VERSION 3.21.1)

set(BUILD_QDS_COMPONENTS ON CACHE BOOL "Build design studio components")
option(LINK_INSIGHT "Link Qt Insight Tracker library" ON)

project(ClusterApp LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

# find_package(QT NAMES Qt6 COMPONENTS Gui Qml Quick)
# find_package(Qt6 REQUIRED COMPONENTS Core Qml Quick)
find_package(Qt6 REQUIRED COMPONENTS Core Gui Quick Qml)

# To build this application you need Qt 6.2.0 or higher
if (Qt6_VERSION VERSION_LESS 6.2.0)
message(FATAL_ERROR "You need Qt 6.2.0 or newer to build the application.")
endif()

#실행파일
qt_add_executable(${CMAKE_PROJECT_NAME} 
    src/main.cpp
    src/CanReceiver.cpp
)

# qt_standard_project_setup() requires Qt 6.3 or higher. See https://doc.qt.io/qt-6/qt-standard-project-setup.html for details.
# if (${QT_VERSION_MINOR} GREATER_EQUAL 3)
# qt6_standard_project_setup()
# endif()

# qt6_add_resources(${PROJECT_NAME} qml.qrc)
qt6_add_resources(qml_resources qml.qrc)
target_sources(${PROJECT_NAME} PRIVATE ${qml_resources})


target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::Gui
    Qt${QT_VERSION_MAJOR}::Quick
    Qt${QT_VERSION_MAJOR}::Qml
)

if (${BUILD_QDS_COMPONENTS})
    include(${CMAKE_CURRENT_SOURCE_DIR}/qmlcomponents)
endif ()

include(${CMAKE_CURRENT_SOURCE_DIR}/qmlmodules)

if (LINK_INSIGHT)
    include(${CMAKE_CURRENT_SOURCE_DIR}/insight)
endif ()

install(TARGETS ${CMAKE_PROJECT_NAME}
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
