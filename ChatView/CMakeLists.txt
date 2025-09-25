add_library(QodeAssistChatView STATIC
    ChatWidget.hpp ChatWidget.cpp
    ChatModel.hpp ChatModel.cpp
    ChatRootView.hpp ChatRootView.cpp
    ClientInterface.hpp ClientInterface.cpp
    MessagePart.hpp
    ChatUtils.h ChatUtils.cpp
    ChatSerializer.hpp ChatSerializer.cpp
    ChatView.hpp ChatView.cpp
)

qt5_add_resources(QodeAssistChatView_RESOURCES ${CMAKE_CURRENT_SOURCE_DIR}/chatview.qrc)
target_sources(QodeAssistChatView PRIVATE ${QodeAssistChatView_RESOURCES})

target_link_libraries(QodeAssistChatView
    PUBLIC
    Qt::Widgets
    Qt::Quick
    Qt::QuickWidgets
    Qt::Network
    QtCreator::Core
    QtCreator::Utils
    LLMCore
    QodeAssistSettings
    Context
)

target_include_directories(QodeAssistChatView
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
)
