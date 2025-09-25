add_library(TaskFlowEditorplugin STATIC
    FlowEditor.hpp FlowEditor.cpp
    FlowsModel.hpp FlowsModel.cpp
    TaskItem.hpp TaskItem.cpp
    FlowItem.hpp FlowItem.cpp
    TaskModel.hpp TaskModel.cpp
    TaskPortItem.hpp TaskPortItem.cpp
    TaskPortModel.hpp TaskPortModel.cpp
    TaskConnectionsModel.hpp TaskConnectionsModel.cpp
    TaskConnectionItem.hpp TaskConnectionItem.cpp
    GridBackground.hpp GridBackground.cpp
)

qt5_add_resources(TaskFlowEditorplugin_RESOURCES ${CMAKE_CURRENT_SOURCE_DIR}/taskflow_editor.qrc)
target_sources(TaskFlowEditorplugin PRIVATE ${TaskFlowEditorplugin_RESOURCES})

target_link_libraries(TaskFlowEditorplugin
    PUBLIC
    Qt::Quick
    PRIVATE
    TaskFlowCore
)

target_include_directories(TaskFlowEditorplugin
    PUBLIC
    ${CMAKE_CURRENT_LIST_DIR}
)
