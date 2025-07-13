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

# For Windows
if (WIN32 AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
    set(MY_TARGET "FuncDemoOCC")
    set(QT_INSTALL_PATH  ${Qt6_DIR}/../../../)
    set(DEBUG_SUFFIX)
    if (MSVC AND CMAKE_BUILD_TYPE MATCHES "Debug")
        set(DEBUG_SUFFIX "d")
    endif()

    message("herhe: ${QT_INSTALL_PATH}")
    if(EXISTS "${QT_INSTALL_PATH}/plugins/platforms/qwindows${DEBUG_SUFFIX}.dll")
        add_custom_command(TARGET ${MY_TARGET} POST_BUILD
                COMMAND ${CMAKE_COMMAND} -E make_directory
                "$<TARGET_FILE_DIR:${MY_TARGET}>/plugins/platforms/")
        add_custom_command(TARGET ${MY_TARGET} POST_BUILD
                COMMAND ${CMAKE_COMMAND} -E copy
                "${QT_INSTALL_PATH}/plugins/platforms/qwindows${DEBUG_SUFFIX}.dll"
                "$<TARGET_FILE_DIR:${MY_TARGET}>/plugins/platforms/")
    endif()

endif()

if (WIN32)
    add_custom_command(
            TARGET FuncDemoOCC POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_if_different $<TARGET_RUNTIME_DLLS:FuncDemoOCC> $<TARGET_FILE_DIR:FuncDemoOCC>
            COMMAND_EXPAND_LISTS
    )
endif ()