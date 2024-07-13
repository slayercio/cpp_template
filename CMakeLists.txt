cmake_minimum_required(VERSION 3.20)

if(DEFINED ENV{PROJECT_NAME})
    set(PROJ_NAME ENV{PROJECT_NAME})
else()
    set(PROJ_NAME cpp_template)
endif()

set(LIB_INCLUDE_DIR include/${PROJ_NAME}/lib)
set(EXE_INCLUDE_DIR include/${PROJ_NAME}/exe)

file(MAKE_DIRECTORY ${LIB_INCLUDE_DIR} ${EXE_INCLUDE_DIR})

project(${PROJ_NAME} CXX)

if(DEFINED ENV{CPP_STANDARD})
    set(CMAKE_CXX_STANDARD ENV{CPP_STANDARD})
else()
    set(CMAKE_CXX_STANDARD 20)
endif()

option(BUILD_TEST_EXEC   "Build test executable"    ON)
option(BUILD_SHARED_LIBS "Build shared libraries"   ON)
option(BUILD_STATIC_LIBS "Build static libraries"   OFF)
option(BUILD_EXECUTABLE  "Build executable"         ON)

set(SHARED_NAME     ${PROJ_NAME}_shared)
set(STATIC_NAME     ${PROJ_NAME}_static)
set(EXECUTABLE_NAME ${PROJ_NAME}_exec)
set(TEST_NAME       ${PROJ_NAME}_test)

if(DEFINED ENV{LIB_SRCDIR})
    set(LIB_SRCDIR ENV{LIB_SRCDIR})
else()
    set(LIB_SRCDIR src/lib)
endif()

file(MAKE_DIRECTORY ${LIB_SRCDIR})

FILE(GLOB lib_files ${LIB_SRCDIR}/**.cpp)

if(BUILD_SHARED_LIBS)
    add_library(
        ${SHARED_NAME}
        SHARED
        ${lib_files}
    )

    target_include_directories(${SHARED_NAME} PUBLIC ${LIB_INCLUDE_DIR})
endif()

if(BUILD_STATIC_LIBS)
    add_library(
        ${STATIC_NAME}
        STATIC
        ${lib_files}
    )

    target_include_directories(${STATIC_NAME} PUBLIC ${LIB_INCLUDE_DIR})
endif()

if(DEFINED ENV{EXE_SRCDIR})
    set(EXE_SRCDIR ENV{EXE_SRCDIR})
else()
    set(EXE_SRCDIR src/exe)
endif()

file(MAKE_DIRECTORY ${EXE_SRCDIR})

FILE(GLOB exe_files ${EXE_SRCDIR}/**.cpp)

if(BUILD_EXECUTABLE)
    add_executable(
        ${EXECUTABLE_NAME}
        ${exe_files}
    )

    target_include_directories(${EXECUTABLE_NAME} PUBLIC ${LIB_INCLUDE_DIR} ${EXE_INCLUDE_DIR})
endif()

if(WIN32 AND MSVC)
    if(BUILD_TEST_EXEC)

    endif()

    if(BUILD_SHARED_LIBS)
        target_compile_definitions(${SHARED_NAME} PUBLIC TPAPI_EXPORT=__declspec\(dllexport\) TPAPI_IMPORT=__declspec\(dllimport\) TPAPI=TPAPI_EXPORT)
    endif()

    if(BUILD_STATIC_LIBS)
        target_compile_definitions(${STATIC_NAME} PUBLIC TPAPI_EXPORT=__declspec\(dllexport\) TPAPI_IMPORT=__declspec\(dllimport\) TPAPI=TPAPI_EXPORT)
    endif()

    if(BUILD_EXECUTABLE)
        target_compile_definitions(${EXECUTABLE_NAME} PUBLIC TPAPI_EXPORT=__declspec\(dllexport\) TPAPI_IMPORT=__declspec\(dllimport\) TPAPI=TPAPI_IMPORT)
        
        if(BUILD_STATIC_LIBS)
            target_link_libraries(${EXECUTABLE_NAME} ${STATIC_NAME})
        elseif(BUILD_SHARED_LIBS)
            target_link_libraries(${EXECUTABLE_NAME} ${SHARED_NAME})
        endif()
    endif()
else()
    if(BUILD_SHARED_LIBS)
        target_compile_definitions(${SHARED_NAME} PUBLIC TPAPI_EXPORT TPAPI_IMPORT TPAPI=TPAPI_EXPORT)
    endif()

    if(BUILD_STATIC_LIBS)
        target_compile_definitions(${STATIC_NAME} PUBLIC TPAPI_EXPORT TPAPI_IMPORT TPAPI=TPAPI_EXPORT)
    endif()

    if(BUILD_EXECUTABLE)
        target_compile_definitions(${EXECUTABLE_NAME} PUBLIC TPAPI_EXPORT TPAPI_IMPORT TPAPI=TPAPI_IMPORT)
        
        if(BUILD_STATIC_LIBS)
            target_link_libraries(${EXECUTABLE_NAME} ${STATIC_NAME})
        elseif(BUILD_SHARED_LIBS)
            target_link_libraries(${EXECUTABLE_NAME} ${SHARED_NAME})
        endif()
    endif()
endif()