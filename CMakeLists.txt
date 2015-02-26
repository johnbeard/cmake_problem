project("Cmake minimal test")
cmake_minimum_required(VERSION 2.8)

set(CONVERTED_DIR ${CMAKE_CURRENT_BINARY_DIR}/converted)
set(INPUT_FILE ${CMAKE_CURRENT_SOURCE_DIR}/inputs/input1.bin)

add_custom_command(
    OUTPUT ${CONVERTED_DIR}
    COMMENT "Creating compiled output dir: ${CONVERTED_DIR}"
    COMMAND mkdir -p ${CONVERTED_DIR}
)

file (GLOB inputFiles  ${CMAKE_CURRENT_SOURCE_DIR}/inputs/*.bin)

message("Input files: ${inputFiles}")

set(convertedFiles)
foreach(_inputFile ${inputFiles})

    get_filename_component (filename ${_inputFile} NAME_WE)
    set(_convertedFile ${CONVERTED_DIR}/${filename}.converted)

    add_custom_command(
        OUTPUT ${_convertedFile}
        DEPENDS ${_inputFile} ${CONVERTED_DIR}
        COMMAND cp ${_inputFile} ${_convertedFile}
    )

    list(APPEND convertedFiles ${_convertedFile})

endforeach()

message("Converted files: ${convertedFiles}")

add_custom_command(
    OUTPUT main.c
    DEPENDS ${convertedFiles}
    COMMAND cat ${CONVERTED_DIR}/*.converted > ${CMAKE_CURRENT_BINARY_DIR}/main.c
)

add_executable(test main.c)