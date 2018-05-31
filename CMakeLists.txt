cmake_minimum_required(VERSION 3.6)

project(espa-python-library NONE)
set (${PROJECT_NAME}_VERSION_MAJOR 1)
set (${PROJECT_NAME}_VERSION_MINOR 0)
set (${PROJECT_NAME}_VERSION_PATCH 0)
set (VERSION ${${PROJECT_NAME}_VERSION_MAJOR}.${${PROJECT_NAME}_VERSION_MINOR}.${${PROJECT_NAME}_VERSION_PATCH})

set (PACKAGE ${PROJECT_NAME})

set(CPACK_PACKAGE_VERSION ${VERSION})
set(CPACK_GENERATOR "TGZ")
set(CPACK_SOURCE_GENERATOR "TGZ")
set(CPACK_SOURCE_IGNORE_FILES "/build/;/.git/;/.gz/;~$;${CPACK_SOURCE_IGNORE_FILES}")
include(CPack)

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
endif ()
