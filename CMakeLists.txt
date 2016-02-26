project(foo)
cmake_minimum_required(VERSION 2.8.12)

add_custom_command(OUTPUT foo.h
  COMMAND cp "${CMAKE_CURRENT_SOURCE_DIR}/foo.h.gen" "${CMAKE_CURRENT_BINARY_DIR}/foo.h"
  DEPENDS foo.h.gen
  WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}"
)

# Adding "foo.h" here causes the build to succeed but
# the build system is broken because if foo.h.gen is
# changed ``foo.h`` is regenerated but the sources that
# depend on it are NOT REBUILT!
#
# Why can't CMake work out that ``foo.c`` and ``foo2.c`` depend
# on ``foo.h``??
add_library(foo foo.c foo2.c)
target_include_directories(foo PRIVATE "${CMAKE_CURRENT_BINARY_DIR}")

# Note trying this doesn't work either.
# The build will succeed with this addition
# but if ``foo.h.gen`` changes then ``foo.h``
# is regenerated but the sources are not rebuilt
# add_custom_target(foo_custom DEPENDS foo.h)
# add_dependencies(foo foo_custom)
