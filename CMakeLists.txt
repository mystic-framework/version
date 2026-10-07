cmake_minimum_required(VERSION 3.30)

# Import CMake modules
include(cmake/MysticImportCMake.cmake)
mystic_import_cmake()

# Banner
include(MysticAsciiBanner)
mystic_ascii_banner("Mystic FWK: Version")

# Project Configuration
include(MysticProject)
mystic_project()
project(${MYSTICVERSION_PROJECT_ARGUMENTS})

# Setup Options
include(MysticSetupOptions)
mystic_setup_options()
mystic_setup_third_party_options("Catch2")

# Include Third Party
include(MysticIncludeThirdParty)
mystic_include_third_party()

# Target
add_library(mystic_version ${MYSTICVERSION_LIB_TYPE})
add_library(Mystic::Version ALIAS mystic_version)

# File Groups
file(GLOB_RECURSE MYSTICVERSION_PUBLIC_MODULES  CONFIGURE_DEPENDS "source/public/*.cppm")
file(GLOB_RECURSE MYSTICVERSION_PUBLIC_HEADERS  CONFIGURE_DEPENDS "source/public/*.hpp")
file(GLOB_RECURSE MYSTICVERSION_PRIVATE_MODULES CONFIGURE_DEPENDS "source/private/*.cppm")
file(GLOB_RECURSE MYSTICVERSION_PRIVATE_HEADERS CONFIGURE_DEPENDS "source/private/*.hpp")
file(GLOB_RECURSE MYSTICVERSION_PRIVATE_SOURCES CONFIGURE_DEPENDS "source/private/*.cpp")
file(GLOB_RECURSE MYSTICVERSION_TEST_FILES "test/*.test.cpp")

# Compilation Units
target_sources(mystic_version
  PUBLIC
    FILE_SET CXX_MODULES FILES
      ${MYSTICVERSION_PUBLIC_MODULES}
      ${MYSTICVERSION_PRIVATE_MODULES}
    FILE_SET HEADERS FILES
      ${MYSTICVERSION_PUBLIC_HEADERS}
  PRIVATE
    ${MYSTICVERSION_PRIVATE_HEADERS}
    ${MYSTICVERSION_PRIVATE_SOURCES}
)

# Include Directories
include(GNUInstallDirs)
target_include_directories(mystic_version
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/source/public>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/mystic>
  PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/source/private
)

# Compile Options
include(MysticCompileOptions)
mystic_compile_options(mystic_version)

# Export Everything on Windows (remove it later)
set_target_properties(mystic_version PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS ON)

# Testing
include(MysticTest)
mystic_enable_testing()
mystic_test(mystic_version_test
  SOURCES ${MYSTICVERSION_TEST_FILES}
  DEPENDENCIES Mystic::Version
)

# Coverage
include(MysticCoverage)
mystic_coverage(mystic_version mystic_version_test)

# Linting
include(MysticLint)
mystic_lint(mystic_version)

# Sanitizers
include(MysticSanitizers)
mystic_sanitizers(mystic_version)

# Installation
include(MysticInstall)
mystic_install(mystic_version)
