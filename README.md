# C++20 CMake Template


### Available options
- BUILD_TEST_EXEC - whether to build tests using google test
- BUILD_SHARED_LIBS - whether to build shared library
- BUILD_STATIC_LIBS - whether to build static library
- BUILD_EXECUTABLE  - whether to build executable

### Available environment variables
- PROJECT_NAME - name of the project (default cpp_template)
- CPP_STANDARD - version of c++ (default 20)
- LIB_SRCDIR   - location of library sources (default src/lib)
- EXE_SRCDIR   - location of exe sources (default src/exe)