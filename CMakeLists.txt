cmake_minimum_required(VERSION 3.30)
project(FuncDemoOCC)

set(CMAKE_CXX_STANDARD 17)

# Find OpenCASCADE
if(OpenCASCADE_DIR)
    find_package(OpenCASCADE REQUIRED PATHS ${OpenCASCADE_DIR} NO_DEFAULT_PATH)
else()
    find_package(OpenCASCADE REQUIRED)
endif()

# Find QT6
find_package(Qt6 REQUIRED COMPONENTS
        Core
        Gui
        Widgets )

# Setup QT MOC, RCC and UIC
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Setup QT resources
set(RESOURCE_FILES
        src/FuncDemo.qrc
    )
qt_add_resources(RESOURCES_RCC ${RESOURCE_FILES})

# FuncDemoOCC executable
add_executable(FuncDemoOCC
        src/main.cpp
        src/mainwindow.cpp
        src/CircleDriver.cpp
        src/PrismDriver.cpp
        src/ConeDriver.cpp
        src/BaseDriver.cpp
        src/PointDriver.cpp
        src/CylinderDriver.cpp
        src/SimpleDriver.cpp
        src/ShapeSaverDriver.cpp
        src/graphwidget.cpp
        src/FThread.cpp
        src/node.cpp
        src/edge.cpp
        ${RESOURCES_RCC}
    )

target_include_directories(FuncDemoOCC PRIVATE src)

# Link OCC targets
target_link_libraries(FuncDemoOCC PRIVATE
        ${OpenCASCADE_FoundationClasses_LIBRARIES}
        ${OpenCASCADE_ModelingData_LIBRARIES}
        ${OpenCASCADE_ModelingAlgorithms_LIBRARIES}
        ${OpenCASCADE_DataExchange_LIBRARIES}
        ${OpenCASCADE_ApplicationFramework_LIBRARIES}
    )

# Link QT targets
target_link_libraries(FuncDemoOCC PRIVATE
        Qt::Core
        Qt::Gui
        Qt::Widgets
    )
