project(websocketclient)

# -----------------------------------------------------------------------
# Global set up
#
set(CMAKE_CXX_STANDARD 11)
if( WIN32 )
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4503 /wd4267 /wd4244 /wd4996")
endif()
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

#------------------------------------------------------------------------
# OpenSSL included in libwebrtc
#
find_package(libwebrtc REQUIRED)

#------------------------------------------------------------------------
# Asio
#
include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/third_party/asio/include )

#------------------------------------------------------------------------
# WebSocket
#
set( websocketclient_SOURCES 
  JanusWebsocketClientImpl.cpp
  SpankChainWebsocketClientImpl.cpp
  WebsocketClient.cpp
)
if( WIN32 )
  set( websocketclient_SOURCES ${websocketclient_SOURCES} dllmain.cpp )
  add_definitions( -DWEBSOCKETCLIENT_EXPORTS )
elseif( APPLE )
  add_definitions( -DWEBSOCKETCLIENT_EXPORTS )
endif()

set( websocketclient_HEADERS
  json.hpp
  JanusWebsocketClientImpl.h
  SpankChainWebsocketClientImpl.h
  WebsocketClient.h
  )

add_library( websocketclient SHARED
  ${websocketclient_SOURCES}
  ${websocketclient_HEADERS}
  )

# why ?
target_link_libraries( websocketclient
  ${WEBRTC_LIBRARIES}
)
install_obs_plugin(websocketclient)

if ( APPLE )
  target_link_libraries( websocketclient
    /usr/local/lib/libssl.dylib
    /usr/local/lib/libcrypto.dylib
  )
endif()

#-------------------------------------------------------------
# Libwebrtc with OpenSSL
#

# The developers used an installer for libwebrtc with OpenSSL
# Change destination if needed
# Works only if OpenSSL is built as a shared library
set( libwebrtc_rootdir ${libwebrtc_DIR}/..)
if ( UNIX AND NOT APPLE )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libssl.a
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libcrypto.a
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libwebrtc.a
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libssl.so.1.1
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libcrypto.so.1.1
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
elseif ( APPLE )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libssl.a
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libcrypto.a
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libwebrtc.a
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libssl.1.1.dylib
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libcrypto.1.1.dylib
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
else ( WIN32 )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libssl.lib
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/libcrypto.lib
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/lib/webrtc.lib
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Libraries )
  install(
    FILES       ${libwebrtc_rootdir}/bin/libssl-1_1-x64.dll
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Runtime )
  install(
    FILES       ${libwebrtc_rootdir}/bin/libcrypto-1_1-x64.dll
    DESTINATION ${OBS_EXECUTABLE_DESTINATION} COMPONENT   Runtime )
endif ()
