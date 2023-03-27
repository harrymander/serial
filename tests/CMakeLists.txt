if(UNIX)
    include(FetchContent)

    fetchcontent_declare(
        googletest
        GIT_REPOSITORY https://github.com/google/googletest.git
        GIT_TAG b796f7d44681514f58a683a3a71ff17c94edb0c1
    )

    fetchcontent_makeavailable(googletest)

    include(GoogleTest)
    enable_testing()

    add_executable(${PROJECT_NAME}-test unix_serial_tests.cc)
    target_link_libraries(
        ${PROJECT_NAME}-test
        PRIVATE ${PROJECT_NAME} GTest::gtest_main
    )
    if(NOT APPLE)
        target_link_libraries(${PROJECT_NAME}-test PRIVATE util)
    endif()

    gtest_discover_tests(${PROJECT_NAME}-test)

    if(NOT APPLE)  # these tests are unreliable on macOS
        add_executable(${PROJECT_NAME}-test-timer unit/unix_timer_tests.cc)
        target_link_libraries(
            ${PROJECT_NAME}-test-timer
            PRIVATE ${PROJECT_NAME} GTest::gtest_main
        )
        gtest_discover_tests(${PROJECT_NAME}-test-timer)
    endif()
endif()
