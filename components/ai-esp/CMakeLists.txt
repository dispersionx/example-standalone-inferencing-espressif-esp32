# set(MODEL_FOLDER ${CMAKE_CURRENT_SOURCE_DIR})
# set(EI_SDK_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/../components/ai-esp/edge-impulse-sdk)

if(NOT CMAKE_BUILD_EARLY_EXPANSION)
# add_definitions(-DEI_CLASSIFIER_TFLITE_ENABLE_ESP_NN=1) # enables ESP-NN optimizations by Espressif
# disabled by default - enable if you use ESP32S3
add_definitions(-DEI_CLASSIFIER_TFLITE_ENABLE_ESP_NN_S3=1) # enables ESP-NN S3 optimizations by Espressif
set(MODEL_FOLDER ${CMAKE_CURRENT_SOURCE_DIR})
set(EI_SDK_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/edge-impulse-sdk)
else()
set(MODEL_FOLDER ${CMAKE_CURRENT_SOURCE_DIR})
set(EI_SDK_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/../components/ai-esp/edge-impulse-sdk)
endif()

OPTION(DEFINE_DEBUG
    "Build the project using debugging code"
    OFF)
IF(DEFINE_DEBUG)
    MESSAGE("Adding Debug flag...")
    ADD_DEFINITIONS(-DDEBUG)
    SET(CMAKE_BUILD_TYPE Debug)
    MESSAGE("Build type is " ${CMAKE_BUILD_TYPE})
ENDIF(DEFINE_DEBUG)

set(include_dirs
    ${MODEL_FOLDER}
    ${MODEL_FOLDER}/tflite-model
    ${MODEL_FOLDER}/model-parameters
    ${EI_SDK_FOLDER}
)

include(${EI_SDK_FOLDER}/cmake/utils.cmake)

RECURSIVE_FIND_FILE_EXCLUDE_DIR(SOURCE_FILES "${EI_SDK_FOLDER}" "CMSIS" "*.cpp")
RECURSIVE_FIND_FILE_EXCLUDE_DIR(MODEL_FILES "${MODEL_FOLDER}/tflite-model" "CMSIS" "*.cpp")
RECURSIVE_FIND_FILE_EXCLUDE_DIR(CC_FILES "${EI_SDK_FOLDER}" "CMSIS" "*.cc")
RECURSIVE_FIND_FILE_EXCLUDE_DIR(S_FILES "${EI_SDK_FOLDER}" "CMSIS" "*.s")
RECURSIVE_FIND_FILE_EXCLUDE_DIR(C_FILES "${EI_SDK_FOLDER}" "CMSIS" "*.c")

list(APPEND SOURCE_FILES ${S_FILES})
list(APPEND SOURCE_FILES ${C_FILES})
list(APPEND SOURCE_FILES ${CC_FILES})
list(APPEND SOURCE_FILES ${MODEL_FILES})

idf_component_register(SRCS "${SOURCE_FILES}"
                       INCLUDE_DIRS "${include_dirs}"
                       REQUIRES esp_timer)