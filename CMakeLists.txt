cmake_minimum_required(VERSION 3.8)

set(CMAKE_JAR_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/jars" CACHE PATH "Global runtime dir")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin" CACHE PATH "Global runtime dir")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib" CACHE PATH "Global library dir")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib" CACHE PATH "Global archive dir")

find_package(Java 24 REQUIRED)
include(UseJava)

function(add_java_jar_target target_name sources main_class)
  add_jar(${target_name}
    ${sources}
    ENTRY_POINT ${main_class}
  )

  add_custom_command(TARGET ${target_name} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "${CMAKE_JAR_OUTPUT_DIRECTORY}"
    COMMAND ${CMAKE_COMMAND} -E copy
      "${CMAKE_CURRENT_BINARY_DIR}/${target_name}.jar"
      "${CMAKE_JAR_OUTPUT_DIRECTORY}/${target_name}.jar"
  )
endfunction()

add_subdirectory(BranchNetDB)

add_custom_target(run
    COMMAND ${Java_JAVA_EXECUTABLE} 
            -cp "${CMAKE_JAR_OUTPUT_DIRECTORY}/*"   # include all jars
            BranchNetDB                                # your main class
    DEPENDS BranchNetDB                             # rebuild JARs first
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
)