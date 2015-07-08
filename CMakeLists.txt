cmake_minimum_required(VERSION 2.8.12.2)
project(cmake_tutorial)

# The version number.

set(cmake_tutorial_VERSION_MAJOR 1)
set(cmake_tutorial_VERSION_MINOR 0)

# configure a header file to pass some of the CMake settings
# to the source code

configure_file(
    "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
    "${PROJECT_BINARY_DIR}/TutorialConfig.h"
)

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h

include_directories("${PROJECT_BINARY_DIR}")

# should we use our own math functions?

option(USE_MYMATH
       "Use tutorial provided math implementation" ON)
        
# add the MathFunctions library?
#

if(USE_MYMATH)
    include_directories("${PROJECT_SOURCE_DIR}/MathFunctions")
    add_subdirectory(MathFunctions)
    set(EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif(USE_MYMATH)

# add the executable

add_executable(cmake_tutorial main.cpp)
target_link_libraries(cmake_tutorial ${EXTRA_LIBS})

# add the install targets

install (TARGETS cmake_tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h" DESTINATION include)

# does the application run

add_test(TutorialRuns cmake_tutorial 25)

# does it sqrt of 25

add_test(TutorialComp25 cmake_tutorial 25)

set_tests_properties(TutorialComp25
                    PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")

# does it handle negative numbers

add_test(TutorialNegative cmake_tutorial -25)
set_tests_properties(TutorialNegative
                     PROPERTIES PASS_REGULAR_EXPRESSION "-25 is 0")

# does it handle small numbers

add_test(TutorialSmall cmake_tutorial 0.0001)
set_tests_properties(TutorialSmall
                     PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")

# does the usage message work?

add_test(TutorialUsage cmake_tutorial)
set_tests_properties(TutorialUsage
                     PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number")
