cmake_minimum_required(VERSION 3.10.0)

project(sole
  VERSION 1.0.1
  DESCRIPTION "Sole is a lightweight C++11 library to generate universally unique identificators (UUID), both v1 and v4."
  HOMEPAGE_URL "https://github.com/AronRubin/sole"
)

message(STATUS "Configured for: ${CMAKE_BUILD_TYPE}")

set(CMAKE_DEBUG_POSTFIX d)

set(CMAKE_CXX_STANDARD 11)

if(POLICY CMP0074)
  cmake_policy(SET CMP0074 NEW)
endif()
if(POLICY CMP0077)
  cmake_policy(SET CMP0077 NEW)
endif()


add_library(sole SHARED sole.cpp)
set_property(TARGET sole PROPERTY SOVERSION 1.0.0)
target_compile_features(sole PUBLIC cxx_std_11)
set_property(TARGET sole PROPERTY PUBLIC_HEADER sole.hpp)
if(WIN32)
  target_link_libraries(sole PUBLIC Iphlpapi)
endif()

include(GNUInstallDirs)
set(INCLUDE_INSTALL_DIR "${CMAKE_INSTALL_INCLUDEDIR}/sole" )
set(CONFIG_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}/cmake/sole" )
set(RUNTIME_INSTALL_DIR "${CMAKE_INSTALL_BINDIR}" )
set(LIB_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}" )
set(DATA_INSTALL_DIR "${CMAKE_INSTALL_DATADIR}/sole" )

install(TARGETS sole EXPORT soleTargets
  PUBLIC_HEADER DESTINATION ${INCLUDE_INSTALL_DIR}
  RUNTIME DESTINATION ${RUNTIME_INSTALL_DIR}
  LIBRARY DESTINATION ${LIB_INSTALL_DIR}
  ARCHIVE DESTINATION ${LIB_INSTALL_DIR}
  RESOURCE DESTINATION ${DATA_INSTALL_DIR}
)

include(CMakePackageConfigHelpers)
file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake.in"
  "@PACKAGE_INIT@\ninclude(\${CMAKE_CURRENT_LIST_DIR}/\@PROJECT_NAME\@Targets.cmake)\n")
configure_package_config_file(
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
  INSTALL_DESTINATION
    ${CMAKE_INSTALL_PREFIX}/${CONFIG_INSTALL_DIR}
  PATH_VARS
    INCLUDE_INSTALL_DIR
    CONFIG_INSTALL_DIR
    RUNTIME_INSTALL_DIR
    LIB_INSTALL_DIR
    DATA_INSTALL_DIR
)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/soleConfigVersion.cmake"
  COMPATIBILITY
    SameMajorVersion
)

# export targets for find_package config mode
install(EXPORT soleTargets DESTINATION ${CONFIG_INSTALL_DIR})

install(
  FILES
    ${CMAKE_CURRENT_BINARY_DIR}/soleConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/soleConfigVersion.cmake
  DESTINATION
    ${CMAKE_INSTALL_PREFIX}/${CONFIG_INSTALL_DIR}
)

include(FeatureSummary)
feature_summary(WHAT ALL)

