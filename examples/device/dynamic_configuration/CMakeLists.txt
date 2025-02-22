# use directory name for project id
get_filename_component(PROJECT ${CMAKE_CURRENT_SOURCE_DIR} NAME)
set(PROJECT ${BOARD}-${PROJECT})

# TOP is absolute path to root directory of TinyUSB git repo
set(TOP "../../..")
get_filename_component(TOP "${TOP}" REALPATH)

# Check for -DFAMILY=
if(FAMILY STREQUAL "rp2040")
  cmake_minimum_required(VERSION 3.12)
  set(PICO_SDK_PATH ${TOP}/hw/mcu/raspberrypi/pico-sdk)
  include(${PICO_SDK_PATH}/pico_sdk_init.cmake)
  project(${PROJECT})
  pico_sdk_init()
  add_executable(${PROJECT})

  include(${TOP}/hw/bsp/${FAMILY}/family.cmake)

  # Example source
  target_sources(${PROJECT} PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/src/main.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/msc_disk.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/usb_descriptors.c
  )

  # Example include
  target_include_directories(${PROJECT} PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/src
  )

  # Example defines
  target_compile_definitions(${PROJECT} PUBLIC
    CFG_TUSB_OS=OPT_OS_PICO
  )

else()
  message(FATAL_ERROR "Invalid FAMILY specified")
endif()
