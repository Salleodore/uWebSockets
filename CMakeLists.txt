cmake_minimum_required(VERSION 3.0)
set(CMAKE_BUILD_TYPE Release)
set(CMAKE_CXX_STANDARD 11)
project (µWebSockets)

option(BUILD_SHARED_LIBS "Build shared libraries." ON)

if(NOT BFG_PLATFORM_ANDROID)
	if(NOT LIBUV_INCLUDE_DIR)
		find_path(LIBUV_INCLUDE_DIR uv.h)
	endif()
	if(NOT LIBUV_LIBRARY)
		find_library(LIBUV_LIBRARY NAMES uv uv1)
	endif()
endif()

add_library(uWS src/Extensions.cpp src/Group.cpp src/Networking.cpp src/Hub.cpp src/Node.cpp src/WebSocket.cpp src/HTTPSocket.cpp src/Socket.cpp src/Epoll.cpp)

if(CMAKE_VERSION VERSION_LESS "3.1")
    # CMake 3.0 does not implement "CMAKE_CXX_STANDARD":
    if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        set(CMAKE_CXX_FLAGS "--std=gnu++11 ${CMAKE_CXX_FLAGS}")
    endif()
endif()



target_include_directories(uWS PUBLIC src)
if(NOT BFG_PLATFORM_ANDROID)
	target_include_directories(uWS PUBLIC ${LIBUV_INCLUDE_DIR})
else()
	target_include_directories(uWS PUBLIC ../libuv-cmake/libuv/include)
endif()
target_include_directories(uWS PUBLIC ${ZLIB_PUBLIC_HDRS})             
target_include_directories(uWS PUBLIC ${OPENSSL_INCLUDE_DIR})



if(NOT BFG_PLATFORM_ANDROID)
	target_link_libraries (uWS LINK_PUBLIC ${LIBUV_LIBRARY})
else()
	target_link_libraries (uWS LINK_PUBLIC uv)
endif()
target_link_libraries (uWS LINK_PUBLIC ${OPENSSL_SSL_LIBRARY})
target_link_libraries (uWS LINK_PUBLIC ${OPENSSL_CRYPTO_LIBRARY})
target_link_libraries (uWS LINK_PUBLIC ${ZLIB_LIBRARY})

if (UNIX AND NOT BFG_PLATFORM_ANDROID)
target_link_libraries (uWS LINK_PUBLIC pthread)
install (TARGETS uWS DESTINATION /usr/lib64)
install (FILES src/Extensions.h src/WebSocketProtocol.h src/Networking.h src/WebSocket.h src/Hub.h src/Group.h src/Node.h src/Socket.h src/HTTPSocket.h src/uWS.h DESTINATION /usr/include/uWS)
endif (UNIX)

#add_subdirectory(examples)
