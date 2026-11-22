cmake_minimum_required(VERSION 3.20)

project(TurtleGraphics LANGUAGES CXX)

include(CTest)

find_program(PLANTUML_EXECUTABLE NAMES plantuml)

if(PLANTUML_EXECUTABLE)
    set(TURTLE_DIAGRAM "${CMAKE_CURRENT_BINARY_DIR}/diagrams/turtle.svg")

    add_custom_command(
        OUTPUT "${TURTLE_DIAGRAM}"
        COMMAND "${CMAKE_COMMAND}" -E make_directory "${CMAKE_CURRENT_BINARY_DIR}/diagrams"
        COMMAND "${PLANTUML_EXECUTABLE}" -tsvg -o "${CMAKE_CURRENT_BINARY_DIR}/diagrams"
                "${CMAKE_CURRENT_SOURCE_DIR}/turtle.puml"
        DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/turtle.puml"
        COMMENT "Generating turtle class diagram"
        VERBATIM
    )
    add_custom_target(class_diagram DEPENDS "${TURTLE_DIAGRAM}")
else()
    message(STATUS "PlantUML not found; install it to enable the class_diagram target")
endif()

if(BUILD_TESTING)
    include(FetchContent)

    FetchContent_Declare(
        googletest
        URL https://github.com/google/googletest/archive/refs/tags/v1.16.0.zip
        DOWNLOAD_EXTRACT_TIMESTAMP TRUE
    )
    FetchContent_MakeAvailable(googletest)

    add_executable(turtle_graphics_tests
        tests/smoke_test.cpp
    )
    target_compile_features(turtle_graphics_tests PRIVATE cxx_std_17)
    target_link_libraries(turtle_graphics_tests PRIVATE GTest::gtest_main)

    include(GoogleTest)
    gtest_discover_tests(turtle_graphics_tests)
endif()
