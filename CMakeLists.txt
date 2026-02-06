cmake_minimum_required(VERSION 3.20)

project(OpenVic LANGUAGES CXX)

set(OPENVIC_SCONS_PLATFORM "windows")
set(OPENVIC_SCONS_TARGET "template_debug")
set(OPENVIC_SCONS_ARCH "x86_64")
set(OPENVIC_SCONS_SUFFIX "${OPENVIC_SCONS_PLATFORM}.${OPENVIC_SCONS_TARGET}.${OPENVIC_SCONS_ARCH}")

set(OPENVIC_GODOTCPP_SUFFIX "${OPENVIC_SCONS_SUFFIX}")

set(OPENVIC_BIN_DIR "${CMAKE_CURRENT_SOURCE_DIR}/game/bin/openvic")

file(GLOB_RECURSE OPENVIC_EXTENSION_SOURCES CONFIGURE_DEPENDS
    "extension/src/*.cpp"
)

add_library(openvic SHARED ${OPENVIC_EXTENSION_SOURCES})
set_target_properties(openvic PROPERTIES
    OUTPUT_NAME "openvic"
    PREFIX "lib"
    RUNTIME_OUTPUT_DIRECTORY "${OPENVIC_BIN_DIR}"
    LIBRARY_OUTPUT_DIRECTORY "${OPENVIC_BIN_DIR}"
    ARCHIVE_OUTPUT_DIRECTORY "${OPENVIC_BIN_DIR}"
)

target_compile_features(openvic PRIVATE cxx_std_20)

if(MSVC)
    set_property(TARGET openvic PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")
    target_compile_options(openvic PRIVATE
        /nologo
        /utf-8
        /Zc:preprocessor
        $<$<NOT:$<CONFIG:Debug>>:/O2>
    )
endif()

target_compile_definitions(openvic PRIVATE
    DEBUG_ENABLED
    FOONATHAN_MEMORY=1
    FOONATHAN_MEMORY_VERSION_MAJOR=0
    FOONATHAN_MEMORY_VERSION_MINOR=7
    FOONATHAN_MEMORY_VERSION_PATCH=4
    HOT_RELOAD_ENABLED
    LEXY_HAS_UNICODE_DATABASE=1
    NDEBUG
    NOMINMAX
    OPT_SPEED_TRACE_ENABLED
    SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_INFO
    SPDLOG_COMPILED_LIB
    SPDLOG_FMT_EXTERNAL
    SPDLOG_FWRITE_UNLOCKED
    SPDLOG_NO_EXCEPTIONS
    TYPED_METHOD_BIND
    TYPE_SAFE_ARITHMETIC_POLICY=1
    TYPE_SAFE_ENABLE_ASSERTIONS=0
    TYPE_SAFE_ENABLE_PRECONDITION_CHECKS=1
    TYPE_SAFE_ENABLE_WRAPPER=0
    WINDOWS_ENABLED
    _GLIBCXX_ASSERTIONS
    _HAS_EXCEPTIONS=0
    _LIBCPP_HARDENING_MODE=_LIBCPP_HARDENING_MODE_FAST
    _MSVC_STL_HARDENING=1
)

target_include_directories(openvic PRIVATE
    "extension/src"
    "extension/src/gen"
    "godot-cpp/include"
    "godot-cpp/gen/include"
    "extension/deps/gli"
    "extension/deps/gli/external"
    "extension/deps/openvic-simulation/src"
    "extension/deps/openvic-simulation/deps/openvic-dataloader/include"
    "extension/deps/openvic-simulation/deps/openvic-dataloader/deps/dryad/include"
    "extension/deps/openvic-simulation/deps/openvic-dataloader/deps/fmt/include"
    "extension/deps/openvic-simulation/deps/openvic-dataloader/deps/range-v3/include"
    "extension/deps/openvic-simulation/deps/openvic-dataloader/deps/vmcontainer/lib/include"
    "extension/deps/openvic-simulation/deps/ordered-map/include"
    "extension/deps/openvic-simulation/deps/plf_colony"
    "extension/deps/openvic-simulation/deps/function2/include"
    "extension/deps/openvic-simulation/deps/std_function"
    "extension/deps/openvic-simulation/deps/memory/include"
    "extension/deps/openvic-simulation/deps/spdlog/include"
    "extension/deps/openvic-simulation/deps/xoshiro"
    "extension/deps/openvic-simulation/deps/type_safe/include"
    "extension/deps/openvic-simulation/deps/type_safe/external/debug_assert"
    "extension/deps/openvic-simulation/deps/int128/include"
)

target_link_libraries(openvic PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/godot-cpp/bin/libgodot-cpp.${OPENVIC_GODOTCPP_SUFFIX}.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/bin/libopenvic-simulation.${OPENVIC_SCONS_SUFFIX}.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/openvic-dataloader/bin/libopenvic-dataloader.${OPENVIC_SCONS_SUFFIX}.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/lexy-vdf/bin/liblexy-vdf.${OPENVIC_SCONS_SUFFIX}.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/lexy-vdf/deps/lexy/src/liblexy_file.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/openvic-dataloader/deps/lexy/src/liblexy_file.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/openvic-dataloader/deps/fmt/src/libfmt.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/openvic-dataloader/deps/vmcontainer/lib/src/libvmcontainer.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/memory/src/libmemory.lib"
    "${CMAKE_CURRENT_SOURCE_DIR}/extension/deps/openvic-simulation/deps/spdlog/src/libspdlog.lib"
)
