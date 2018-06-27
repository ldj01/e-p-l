cmake_minimum_required(VERSION 3.6)

project(espa-python-library NONE)

# Set the package version.
include(package_version.cmake)

# Create test dashboard.
include (CTest)

enable_testing()

# Set a flag denoting whether the build is in-source.
if ("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
    set (IN_SOURCE_BUILD TRUE)
endif()

add_test(espa_python python -m unittest discover
         unittests)

if (NOT IN_SOURCE_BUILD)
    add_custom_target(test_links ALL
                      COMMAND cp -r ${CMAKE_CURRENT_SOURCE_DIR}/unittests
                                 ${CMAKE_CURRENT_BINARY_DIR}
                      COMMAND cp -r ${CMAKE_CURRENT_SOURCE_DIR}/espa
                                 ${CMAKE_CURRENT_BINARY_DIR})
    add_custom_target(pylib_links ALL
                      COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/*.py
                                 ${CMAKE_CURRENT_BINARY_DIR}
                      COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/README.md
                                 ${CMAKE_CURRENT_BINARY_DIR})
endif ()

# Determine where to install (ESPA or IPS) based on environment variables.
if (DEFINED ENV{ESPA_PYLIB})
    set (ESPA_PYLIB $ENV{ESPA_PYLIB})
else ()
    set (ESPA_PYLIB $ENV{IASLIB_PYLIB})
endif ()
install(CODE "execute_process(COMMAND python setup.py install
                                      --prefix=${ESPA_PYLIB}
                              WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})")
