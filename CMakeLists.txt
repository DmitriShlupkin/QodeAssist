cmake_minimum_required(VERSION 3.16)

project(QodeAssist)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

find_package(QtCreator REQUIRED COMPONENTS
  Core
  TextEditor
  ProjectExplorer
  LanguageClient
)
find_package(Qt5 COMPONENTS Core Gui Quick Widgets QuickWidgets Qml Concurrent Network Test LinguistTools REQUIRED)
find_package(GTest)


set(_qt_components
  Core
  Gui
  Quick
  QuickWidgets
  Widgets
  Network
  Concurrent
  Qml
  Test
)

foreach(component IN LISTS _qt_components)
  if(TARGET Qt5::${component} AND NOT TARGET Qt::${component})
    add_library(Qt::${component} INTERFACE IMPORTED)
    set_property(TARGET Qt::${component} PROPERTY INTERFACE_LINK_LIBRARIES Qt5::${component})
  endif()
endforeach()

unset(_qt_components)

# IDE_VERSION is defined by QtCreator package
string(REGEX MATCH "([0-9]+)\\.([0-9]+)\\.([0-9]+)" version_match ${IDE_VERSION})
set(QODEASSIST_QT_CREATOR_VERSION_MAJOR ${CMAKE_MATCH_1})
set(QODEASSIST_QT_CREATOR_VERSION_MINOR ${CMAKE_MATCH_2})
set(QODEASSIST_QT_CREATOR_VERSION_PATCH ${CMAKE_MATCH_3})

if(NOT version_match)
    message(FATAL_ERROR "Failed to parse Qt Creator version string: ${IDE_VERSION}")
endif()

message(STATUS "Qt Creator Version: ${QODEASSIST_QT_CREATOR_VERSION_MAJOR}.${QODEASSIST_QT_CREATOR_VERSION_MINOR}.${QODEASSIST_QT_CREATOR_VERSION_PATCH}")

add_definitions(
  -DQODEASSIST_QT_CREATOR_VERSION_MAJOR=${QODEASSIST_QT_CREATOR_VERSION_MAJOR}
  -DQODEASSIST_QT_CREATOR_VERSION_MINOR=${QODEASSIST_QT_CREATOR_VERSION_MINOR}
  -DQODEASSIST_QT_CREATOR_VERSION_PATCH=${QODEASSIST_QT_CREATOR_VERSION_PATCH}
)

add_subdirectory(llmcore)
add_subdirectory(settings)
add_subdirectory(logger)
add_subdirectory(ChatView)
add_subdirectory(context)
if(GTest_FOUND)
  add_subdirectory(test)
endif()

set(QODEASSIST_TRANSLATIONS
    ${CMAKE_CURRENT_SOURCE_DIR}/resources/translations/QodeAssist_en.ts
)

qt5_add_translation(QODEASSIST_QM_FILES ${QODEASSIST_TRANSLATIONS})

set(QODEASSIST_TRANSLATION_QRC ${CMAKE_CURRENT_BINARY_DIR}/QodeAssistTranslations.qrc)
set(_translation_entries "")
foreach(qm ${QODEASSIST_QM_FILES})
  get_filename_component(_qm_name ${qm} NAME)
  string(APPEND _translation_entries "        <file alias=\"${_qm_name}\">${qm}</file>\n")
endforeach()

file(WRITE ${QODEASSIST_TRANSLATION_QRC}
"<RCC>\n    <qresource prefix=\"/translations\">\n${_translation_entries}    </qresource>\n</RCC>\n")

qt5_add_resources(QODEASSIST_TRANSLATION_RCC ${QODEASSIST_TRANSLATION_QRC})
set_source_files_properties(${QODEASSIST_TRANSLATION_RCC} PROPERTIES OBJECT_DEPENDS "${QODEASSIST_QM_FILES}")
add_custom_target(QodeAssistTranslations ALL DEPENDS ${QODEASSIST_QM_FILES})
unset(_translation_entries)

add_qtc_plugin(QodeAssist
  PLUGIN_DEPENDS
    QtCreator::Core
    QtCreator::LanguageClient
    QtCreator::TextEditor
    QtCreator::ProjectExplorer
  DEPENDS
    Qt::Core
    Qt::Gui
    Qt::Quick
    Qt::Widgets
    Qt::Network
    QtCreator::ExtensionSystem
    QtCreator::Utils
    QodeAssistChatView
  SOURCES
    .github/workflows/build_cmake.yml
    .github/workflows/README.md
    README.md
    qodeassist.cpp
    QodeAssistConstants.hpp
    QodeAssisttr.h
    LLMClientInterface.hpp LLMClientInterface.cpp
    templates/Templates.hpp
    templates/CodeLlamaFim.hpp
    templates/Ollama.hpp
    templates/Claude.hpp
    templates/OpenAI.hpp
    templates/MistralAI.hpp
    templates/StarCoder2Fim.hpp
    # templates/DeepSeekCoderFim.hpp
    # templates/CustomFimTemplate.hpp
    templates/Qwen25CoderFIM.hpp
    templates/OpenAICompatible.hpp
    templates/Llama3.hpp
    templates/ChatML.hpp
    templates/Alpaca.hpp
    templates/Llama2.hpp
    templates/CodeLlamaQMLFim.hpp
    templates/GoogleAI.hpp
    templates/LlamaCppFim.hpp
    templates/Qwen3CoderFIM.hpp
    providers/Providers.hpp
    providers/OllamaProvider.hpp providers/OllamaProvider.cpp
    providers/ClaudeProvider.hpp providers/ClaudeProvider.cpp
    providers/OpenAIProvider.hpp providers/OpenAIProvider.cpp
    providers/MistralAIProvider.hpp providers/MistralAIProvider.cpp
    providers/LMStudioProvider.hpp providers/LMStudioProvider.cpp
    providers/OpenAICompatProvider.hpp providers/OpenAICompatProvider.cpp
    providers/OpenRouterAIProvider.hpp providers/OpenRouterAIProvider.cpp
    providers/GoogleAIProvider.hpp providers/GoogleAIProvider.cpp
    providers/LlamaCppProvider.hpp providers/LlamaCppProvider.cpp
    providers/CodestralProvider.hpp providers/CodestralProvider.cpp
    QodeAssist.qrc
    LSPCompletion.hpp
    LLMSuggestion.hpp LLMSuggestion.cpp
    QodeAssistClient.hpp QodeAssistClient.cpp
    chat/ChatOutputPane.h chat/ChatOutputPane.cpp
    chat/NavigationPanel.hpp chat/NavigationPanel.cpp
    ConfigurationManager.hpp ConfigurationManager.cpp
    CodeHandler.hpp CodeHandler.cpp
    UpdateStatusWidget.hpp UpdateStatusWidget.cpp
    widgets/CompletionProgressHandler.hpp widgets/CompletionProgressHandler.cpp
    widgets/ProgressWidget.hpp widgets/ProgressWidget.cpp
    widgets/EditorChatButton.hpp widgets/EditorChatButton.cpp
    widgets/EditorChatButtonHandler.hpp widgets/EditorChatButtonHandler.cpp
    widgets/QuickRefactorDialog.hpp widgets/QuickRefactorDialog.cpp
    QuickRefactorHandler.hpp QuickRefactorHandler.cpp
    tools/ToolsFactory.hpp tools/ToolsFactory.cpp
    tools/ReadProjectFileByNameTool.hpp tools/ReadProjectFileByNameTool.cpp
    tools/ReadVisibleFilesTool.hpp tools/ReadVisibleFilesTool.cpp
    tools/ToolHandler.hpp tools/ToolHandler.cpp
    tools/ListProjectFilesTool.hpp tools/ListProjectFilesTool.cpp
    ${QODEASSIST_TRANSLATION_RCC}
)

add_dependencies(QodeAssist QodeAssistTranslations)

get_target_property(QtCreatorCorePath QtCreator::Core LOCATION)
find_program(QtCreatorExecutable
  NAMES
    qtcreator "Qt Creator"
  PATHS
    "${QtCreatorCorePath}/../../../bin"
    "${QtCreatorCorePath}/../../../MacOS"
  NO_DEFAULT_PATH
)
if (QtCreatorExecutable)
  add_custom_target(RunQtCreator
    COMMAND ${QtCreatorExecutable} -pluginpath $<TARGET_FILE_DIR:QodeAssist>
    DEPENDS QodeAssist
  )
  set_target_properties(RunQtCreator PROPERTIES FOLDER "qtc_runnable")
endif()
