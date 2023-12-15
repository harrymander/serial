cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project(serial VERSION 0.1.0 LANGUAGES CXX)

add_library(${PROJECT_NAME} STATIC src/serial.cc)

target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_20)
if(UNIX)
    target_compile_options(${PROJECT_NAME} PRIVATE -Wall)
else()
    target_compile_options(${PROJECT_NAME} PRIVATE /W4)
endif()
target_include_directories(
    ${PROJECT_NAME}
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

set(SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

## Sources
if(APPLE)
    # If OSX
    target_sources(${PROJECT_NAME} PUBLIC ${SRC_DIR}/impl/unix.cc)
    target_sources(${PROJECT_NAME} PUBLIC ${SRC_DIR}/impl/list_ports/list_ports_osx.cc)
elseif(UNIX)
    # If unix
    target_sources(${PROJECT_NAME} PUBLIC ${SRC_DIR}/impl/unix.cc)
    target_sources(${PROJECT_NAME} PUBLIC ${SRC_DIR}/impl/list_ports/list_ports_linux.cc)
else()
    # If windows
    target_sources(${PROJECT_NAME} PUBLIC ${SRC_DIR}/impl/win.cc)
    target_sources(${PROJECT_NAME} PUBLIC ${SRC_DIR}/impl/list_ports/list_ports_win.cc)
endif()

## Add serial library
if(APPLE)
    find_library(IOKIT_LIBRARY IOKit)
    find_library(FOUNDATION_LIBRARY Foundation)
    target_link_libraries(${PROJECT_NAME} PRIVATE ${FOUNDATION_LIBRARY} ${IOKIT_LIBRARY})
elseif(UNIX)
    find_package(Threads REQUIRED)
    target_link_libraries(${PROJECT_NAME} PRIVATE rt Threads::Threads)
else()
    target_link_libraries(${PROJECT_NAME} PRIVATE setupapi)
endif()

## Tests
option(SERIAL_BUILD_TESTS "Build tests" OFF)
if(SERIAL_BUILD_TESTS)
    add_subdirectory(tests)
endif()
