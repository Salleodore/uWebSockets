cmake_minimum_required(VERSION 3.0)
set(CMAKE_BUILD_TYPE Release)
set(CMAKE_CXX_STANDARD 11)
project (µWebSockets)

option(BUILD_SHARED_LIBS "Build shared libraries." ON)

if(NOT LIBUV_INCLUDE_DIR)
    find_path(LIBUV_INCLUDE_DIR PATHS ../libuv-cmake/libuv/include uv.h)
endif()
if(NOT LIBUV_LIBRARY)
    find_library(LIBUV_LIBRARY NAMES uv uv1 PATHS ../libuv-cmake)
endif()

add_library(uWS src/Extensions.cpp src/Group.cpp src/Networking.cpp src/Hub.cpp src/Node.cpp src/WebSocket.cpp src/HTTPSocket.cpp src/Socket.cpp src/Epoll.cpp)

if(CMAKE_VERSION VERSION_LESS "3.1")
    # CMake 3.0 does not implement "CMAKE_CXX_STANDARD":
    if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        set(CMAKE_CXX_FLAGS "--std=gnu++11 ${CMAKE_CXX_FLAGS}")
    endif()
endif()
target_include_directories(uWS PUBLIC src)

if (NOT UNIX)

target_include_directories(uWS PUBLIC ${LIBUV_INCLUDE_DIR})
target_include_directories(uWS PUBLIC ${ZLIB_INCLUDE_DIRS})
target_include_directories(uWS PUBLIC ${OPENSSL_INCLUDE_DIR})

target_link_libraries (uWS LINK_PUBLIC ${LIBUV_LIBRARY})
target_link_libraries (uWS LINK_PUBLIC ${ZLIB_LIBRARY})
target_link_libraries (uWS LINK_PUBLIC "${OPENSSL_SSL_LIBRARY}")
target_link_libraries (uWS LINK_PUBLIC "${OPENSSL_CRYPTO_LIBRARY}")

elseif (UNIX)

#get_filename_component(LIBSSL-BINARY-LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/../only_android_openssl-1.1.0f_curl-7.54.1/libs/${ANDROID_ABI}/lib/libssl.a" REALPATH)
#get_filename_component(CRYPTO-BINARY-LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/../only_android_openssl-1.1.0f_curl-7.54.1/libs/${ANDROID_ABI}/lib/libcrypto.a" REALPATH)

#get_filename_component(LIBSSL-BINARY-LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/../openssl-1.1.1-pre8/libssl.a" REALPATH)
#get_filename_component(CRYPTO-BINARY-LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/../openssl-1.1.1-pre8/libcrypto.a" REALPATH)

#add_library( SSL-BINARY-LIB
#			 STATIC
#			 IMPORTED)
#
#set_target_properties(SSL-BINARY-LIB
#			 PROPERTIES IMPORTED_LOCATION
#			  ${LIBSSL-BINARY-LOCATION})
#			  
#add_library( CRYPTO-BINARY-LIB
#			 STATIC
#			 IMPORTED)
#
#set_target_properties(CRYPTO-BINARY-LIB
#			 PROPERTIES IMPORTED_LOCATION
#			 ${CRYPTO-BINARY-LOCATION})


			 
target_include_directories(uWS PUBLIC ../libuv-cmake/libuv/include)
target_include_directories(uWS PUBLIC ${ZLIB_PUBLIC_HDRS})			 
target_include_directories(uWS PUBLIC ../openssl-1.1.1-pre8/include)

target_link_libraries (uWS LINK_PUBLIC uv)
#target_link_libraries (uWS LINK_PUBLIC SSL-BINARY-LIB)
target_link_libraries (uWS LINK_PUBLIC ${OPENSSL_SSL_LIBRARY})
#target_link_libraries (uWS LINK_PUBLIC CRYPTO-BINARY-LIB)
target_link_libraries (uWS LINK_PUBLIC ${OPENSSL_CRYPTO_LIBRARY})
target_link_libraries (uWS LINK_PUBLIC zlibstatic)
			 

find_package(Threads REQUIRED)

target_link_libraries (uWS LINK_PUBLIC "${CMAKE_THREAD_LIBS_INIT}")
install (TARGETS uWS DESTINATION /usr/lib64)
install (FILES src/Extensions.h src/WebSocketProtocol.h src/Networking.h src/WebSocket.h src/Hub.h src/Group.h src/Node.h src/Socket.h src/HTTPSocket.h src/uWS.h DESTINATION /usr/include/uWS)
endif ()

#add_subdirectory(examples)
