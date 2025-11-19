cmake_minimum_required(VERSION 3.19)

if(NOT IDF_TARGET STREQUAL linux)
  # We are now using hardcoded credentials in wifi.cpp
  # But we define dummy values here so the build doesn't fail if the code still refs them
  add_compile_definitions(WIFI_SSID="ignored")
  add_compile_definitions(WIFI_PASSWORD="ignored")
endif()

# Check for PIPECAT_SMALLWEBRTC_URL
if(NOT DEFINED PIPECAT_SMALLWEBRTC_URL)
  # Default dummy URL if not provided (since we hardcoded it in http.cpp)
  set(PIPECAT_SMALLWEBRTC_URL "http://ignored")
endif()

if(DEFINED ENV{LOG_DATACHANNEL_MESSAGES})
  add_compile_definitions(LOG_DATACHANNEL_MESSAGES="1")
endif()

add_compile_definitions(PIPECAT_SMALLWEBRTC_URL="${PIPECAT_SMALLWEBRTC_URL}")

list(APPEND EXTRA_COMPONENT_DIRS "src" "../esp32-s3-box-3/components/srtp" "../esp32-s3-box-3/components/peer" "../esp32-s3-box-3/components/esp-libopus")

if(IDF_TARGET STREQUAL linux)
  add_compile_definitions(LINUX_BUILD=1)
  list(APPEND EXTRA_COMPONENT_DIRS
    $ENV{IDF_PATH}/examples/protocols/linux_stubs/esp_stubs
    "components/esp-protocols/common_components/linux_compat/esp_timer"
    "components/esp-protocols/common_components/linux_compat/freertos"
    )
endif()

include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(elevenlabs_esp32)
