cmake_minimum_required(VERSION 3.14)
project(ObjectLibraryHelpers)

function(create_object_lib_target_name out-var file-absolute)
    file(TO_CMAKE_PATH "${file-absolute}" cmake-file-absolute) # for portability with Windows
    string(REPLACE "/" "_" object-lib-target "${cmake-file-absolute}")
    string(REPLACE " " "-" object-lib-target "${object-lib-target}")
    string(REGEX REPLACE "^_" "" object-lib-target "${object-lib-target}") # remove leading _

    set(${out-var} "${object-lib-target}" PARENT_SCOPE)
endfunction()

function(create_object_lib_targets)
    foreach(file ${ARGV})
        get_filename_component(file-absolute "${file}" ABSOLUTE)
        create_object_lib_target_name(object-lib-target-name "${file-absolute}")
        add_library(${object-lib-target-name} OBJECT ${file-absolute})
    endforeach()
endfunction()

function(create_object_lib_list out-var)
    set(object-lib-target-names "")

    # skip the 1st positional argument
    set(args ${ARGV})
    list(REMOVE_AT args 0)

    foreach(file ${args})
        get_filename_component(file-absolute "${file}" ABSOLUTE)
        create_object_lib_target_name(object-lib-target-name "${file-absolute}")
        list(APPEND object-lib-target-names ${object-lib-target-name})
    endforeach()

    set(${out-var} "${object-lib-target-names}" PARENT_SCOPE)
endfunction()

function(create_object_lib_list_with_TARGET_OBJECTS out-var)
    set(object-lib-target-names "")

    # skip the 1st positional argument
    set(args ${ARGV})
    list(REMOVE_AT args 0)

    foreach(file ${args})
        get_filename_component(file-absolute "${file}" ABSOLUTE)
        create_object_lib_target_name(object-lib-target-name "${file-absolute}")
        list(APPEND object-lib-target-names "$<TARGET_OBJECTS:${object-lib-target-name}>")
    endforeach()

    set(${out-var} "${object-lib-target-names}" PARENT_SCOPE)
endfunction()
