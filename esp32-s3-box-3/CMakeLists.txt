cmake_minimum_required(VERSION 3.19)

if(NOT IDF_TARGET STREQUAL linux)
  # Check for WIFI_SSID
  if(NOT DEFINED WIFI_SSID)
    if(DEFINED ENV{WIFI_SSID})
      set(WIFI_SSID "$ENV{WIFI_SSID}")
    else()
      message(FATAL_ERROR "WIFI_SSID must be set via cmake arg or environment variable")
    endif()
  endif()

  # Check for WIFI_PASSWORD
  if(NOT DEFINED WIFI_PASSWORD)
    if(DEFINED ENV{WIFI_PASSWORD})
      set(WIFI_PASSWORD "$ENV{WIFI_PASSWORD}")
    else()
      message(FATAL_ERROR "WIFI_PASSWORD must be set via cmake arg or environment variable")
    endif()
  endif()

  add_compile_definitions(WIFI_SSID=\"${WIFI_SSID}\")
  add_compile_definitions(WIFI_PASSWORD=\"${WIFI_PASSWORD}\")
endif()

# Check for PIPECAT_SMALLWEBRTC_URL
if(NOT DEFINED PIPECAT_SMALLWEBRTC_URL)
  if(DEFINED ENV{PIPECAT_SMALLWEBRTC_URL})
    set(PIPECAT_SMALLWEBRTC_URL "$ENV{PIPECAT_SMALLWEBRTC_URL}")
  else()
    message(FATAL_ERROR "PIPECAT_SMALLWEBRTC_URL must be set via cmake arg or environment variable")
  endif()
endif()

if(DEFINED ENV{LOG_DATACHANNEL_MESSAGES})
  add_compile_definitions(LOG_DATACHANNEL_MESSAGES="1")
endif()

add_compile_definitions(PIPECAT_SMALLWEBRTC_URL=\"${PIPECAT_SMALLWEBRTC_URL}\")

list(APPEND EXTRA_COMPONENT_DIRS "src" "components/srtp" "components/peer" "components/esp-libopus")

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
