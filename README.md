# header_compiler

Simple python script to compile c++ library headers into a single file. You can check out [Abulafia](https://github.com/FrancoisChabot/abulafia) to see this in action (results are in `include/abulafia/abulafia_all.h`)

## Why?

This came to be specifically for use with [Compiler Explorer](https://godbolt.org/), which lets you `#include` from http. Using this script allows an arbitrary header-only library to quickly get compiled to a file that can be served like so: [Example](https://godbolt.org/g/jP8rf8)

## Requirements

The script makes a few assumptions:

1. System headers are included using the `<library>` notation.
2. Every single header needed by the library can be found.
3. Include behavior is not dependant on any preprocessing.
4. If a file has `#ifndef` and `#define` at the top, as well as `#endif` at the bottom, then that's a header guard.

## Drawbacks:

1. Does not handle C-Style comments.
2. Does not handle non-system external dependencies.
3. The stripping out of the comments can be problematic for projects with mixed licenses and copyrights. Currently you have to reconciliate it manually in the notice file.

## Usage

### From the command line

```bash
cd my_project/include
python ../path/to/header_compiler.py --input="my_project.h" \
  --output="my_project_single.h"  \
  --notice="path/to/copyright/notice" \
  --header_guard="MY_PROJECT_SINGLE_HEADER_H"
```

### In cmake

```cmake

SET(ALL_FILE ${CMAKE_CURRENT_SOURCE_DIR}/include/my_project/my_project_all.h)

add_custom_command(
  OUTPUT ${ALL_FILE}
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include
  DEPENDS ${GLOB_OF_ALL_HEADERS} 
  COMMAND python ${CMAKE_CURRENT_SOURCE_DIR}/scripts/compile_headers.py 
    --output=${ALL_FILE}
    --notice=${CMAKE_CURRENT_SOURCE_DIR}/misc/notice.txt
    --header_guard="MY_PROJECT_HEADER_GUARD_H_"
    --input=${CMAKE_CURRENT_SOURCE_DIR}/include/my_project/my_project.h)

add_custom_target(generate_all_include ALL DEPENDS ${ALL_FILE})
```
