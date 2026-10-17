cmake_minimum_required(VERSION 4.2)
project(diode_one VERSION 0.1.0)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(cmake/CPM.cmake)

CPMAddPackage(
    NAME JUCE
    GITHUB_REPOSITORY juce-framework/JUCE
    GIT_TAG 9.0.4
)

# RTNeural (Eigen backend)
# No release tags, so pinning to a commit on main
set(RTNEURAL_EIGEN ON  CACHE BOOL "" FORCE)
set(RTNEURAL_XSIMD OFF CACHE BOOL "" FORCE)
set(RTNEURAL_STL   OFF CACHE BOOL "" FORCE)
CPMAddPackage(
    NAME RTNeural
    GITHUB_REPOSITORY jatinchowdhury18/RTNeural
    GIT_TAG 95c3c0f987a6fe903e7eec71e797405dbed7caf7
)

# Embedded model
juce_add_binary_data(DiodeOneModels
    HEADER_NAME DiodeOneModelData.h
    NAMESPACE   DiodeOneModelData
    SOURCES     "${CMAKE_CURRENT_SOURCE_DIR}/resources/models/diode_one.json")

# Plugin
# Debug builds report version 0.0.0 so Logic and the AU registrar re-validate
# the component on every rebuild
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(DIODEONE_PLUGIN_VERSION 0.0.0)
else()
    set(DIODEONE_PLUGIN_VERSION ${PROJECT_VERSION})
endif()

juce_add_plugin(DiodeOne
    VERSION                     ${DIODEONE_PLUGIN_VERSION}
    COMPANY_NAME                "Partial Audio"
    COMPANY_WEBSITE             "https://partialaudio.com"
    BUNDLE_ID                   "com.partialaudio.diodeone"
    IS_SYNTH                    FALSE
    NEEDS_MIDI_INPUT            FALSE
    NEEDS_MIDI_OUTPUT           FALSE
    COPY_PLUGIN_AFTER_BUILD     TRUE
    PLUGIN_MANUFACTURER_CODE    PaLK
    PLUGIN_CODE                 P005
    FORMATS                     AU VST3 Standalone
    PRODUCT_NAME                "Diode ONE")

file(GLOB DIODEONE_SOURCES CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_SOURCE_DIR}/source/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/source/dsp/*.cpp")

target_sources(DiodeOne PRIVATE ${DIODEONE_SOURCES})

target_include_directories(DiodeOne
    PRIVATE
        source
        source/dsp)

target_compile_definitions(DiodeOne
    PUBLIC
        JUCE_USE_CURL=0
        JUCE_VST3_CAN_REPLACE_VST2=0)

target_link_libraries(DiodeOne
    PRIVATE
        DiodeOneModels
        RTNeural
        juce::juce_audio_utils
        juce::juce_dsp
    PUBLIC
        juce::juce_recommended_config_flags
        juce::juce_recommended_lto_flags
        juce::juce_recommended_warning_flags)
