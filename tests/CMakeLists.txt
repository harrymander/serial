include(GoogleTest)

if(UNIX)
    add_executable(${PROJECT_NAME}-test unix_serial_tests.cc)
    target_link_libraries(${PROJECT_NAME}-test PRIVATE ${PROJECT_NAME})
    if(NOT APPLE)
        target_link_libraries(${PROJECT_NAME}-test PRIVATE util)
    endif()

    if(NOT APPLE)  # these tests are unreliable on macOS
      add_executable(${PROJECT_NAME}-test-timer unit/unix_timer_tests.cc)
      target_link_libraries(${PROJECT_NAME}-test-timer PRIVATE ${PROJECT_NAME})
    endif()
endif()
