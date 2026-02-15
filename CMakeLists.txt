cmake_minimum_required(VERSION 3.20)

project(Azul
        VERSION 1.0.2
        DESCRIPTION "Azul's World - student starter project (executable)"
        LANGUAGES CXX
)

include(GNUInstallDirs)

# -----------------------------
# Diagnostics (helpful for students)
# -----------------------------
message(STATUS "Azul build: CMake ${CMAKE_VERSION}")
message(STATUS "Azul build: Generator: ${CMAKE_GENERATOR}")
message(STATUS "Azul build: Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "Azul build: Pointer size: ${CMAKE_SIZEOF_VOID_P} (8=x64, 4=x86)")
message(STATUS "Azul build: Project version: ${PROJECT_VERSION}")

# -----------------------------
# C++ settings
# -----------------------------
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Put executables in a predictable place (nice for IDEs)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# -----------------------------
# Dependencies (SFML + AzulLib)
# -----------------------------
include(FetchContent)

# SFML options to speed up student builds
set(SFML_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(SFML_BUILD_DOC      OFF CACHE BOOL "" FORCE)
set(SFML_BUILD_TEST_SUITE OFF CACHE BOOL "" FORCE)

# ---- SFML ----
FetchContent_Declare(sfml
        GIT_REPOSITORY https://github.com/SFML/SFML.git
        GIT_TAG        2.6.x
)

# ---- AzulLib ----
# Pin to a stable tag so student builds don't drift.
FetchContent_Declare(azullib
        GIT_REPOSITORY https://github.com/pallenfgcu/AzulLib.git
        GIT_TAG        v1.0.1
)

FetchContent_MakeAvailable(sfml azullib)

# -----------------------------
# Sanity checks (better error messages)
# -----------------------------
if (NOT TARGET sfml-graphics OR NOT TARGET sfml-window OR NOT TARGET sfml-system)
    message(FATAL_ERROR
            "SFML targets not found after FetchContent. Expected sfml-graphics, sfml-window, sfml-system."
    )
endif()

if (TARGET Azul::AzulLib)
    set(AZUL_LIB_TARGET Azul::AzulLib)
elseif (TARGET AzulLib)
    set(AZUL_LIB_TARGET AzulLib)
else()
    message(FATAL_ERROR
            "AzulLib target not found after FetchContent.\n"
            "Expected TARGET Azul::AzulLib or AzulLib.\n"
            "If you recently changed tags, delete the build folder (out/) and reconfigure."
    )
endif()

message(STATUS "Azul build: Using AzulLib target: ${AZUL_LIB_TARGET}")

# -----------------------------
# App target
# -----------------------------
add_executable(azul_app
        src/main.cpp
)

target_compile_features(azul_app PRIVATE cxx_std_17)

target_link_libraries(azul_app PRIVATE
        ${AZUL_LIB_TARGET}
)

# -----------------------------
# Runtime dependencies (Windows)
# Copy OpenAL next to the EXE so students can run it easily
# -----------------------------
if (WIN32)
    add_custom_command(
            TARGET azul_app POST_BUILD
            COMMENT "Copy OpenAL DLL for SFML runtime"
            COMMAND ${CMAKE_COMMAND} -E copy_if_different
            "${sfml_SOURCE_DIR}/extlibs/bin/$<IF:$<EQUAL:${CMAKE_SIZEOF_VOID_P},8>,x64,x86>/openal32.dll"
            "$<TARGET_FILE_DIR:azul_app>"
            VERBATIM
    )
endif()
