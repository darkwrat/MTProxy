cmake_minimum_required(VERSION 3.6.1)
project(mtproto-proxy2)

set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--no-undefined")

# https://developers.redhat.com/blog/2018/03/21/compiler-and-linker-flags-gcc/
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -ggdb3 -O3 -pipe -flto -Wall -Werror \
-D_FORTIFY_SOURCE=2 -fstack-protector-strong -grecord-gcc-switches \
-mpclmul -march=core2 -mfpmath=sse -mssse3 -fno-strict-aliasing -fno-strict-overflow -fwrapv")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fexceptions ${CMAKE_C_FLAGS}")

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

find_package(Git REQUIRED)

execute_process(COMMAND ${GIT_EXECUTABLE} rev-parse HEAD
        OUTPUT_VARIABLE GIT_HASH
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        OUTPUT_STRIP_TRAILING_WHITESPACE)

add_executable(mtproto-proxy2
        ${CMAKE_CURRENT_SOURCE_DIR}/common/crc32c.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/pid.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/sha1.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/sha256.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/md5.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/resolver.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/parse-config.c
        ${CMAKE_CURRENT_SOURCE_DIR}/crypto/aesni256.c
        ${CMAKE_CURRENT_SOURCE_DIR}/jobs/jobs.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/mp-queue.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-events.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-msg.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-msg-buffers.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-config.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-crypto-aes.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-crypto-dh.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-timers.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-connections.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-rpc-targets.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-tcp-connections.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-tcp-rpc-common.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-tcp-rpc-client.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-tcp-rpc-server.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-http-server.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/tl-parse.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/common-stats.c
        ${CMAKE_CURRENT_SOURCE_DIR}/engine/engine.c
        ${CMAKE_CURRENT_SOURCE_DIR}/engine/engine-signals.c
        ${CMAKE_CURRENT_SOURCE_DIR}/engine/engine-net.c
        ${CMAKE_CURRENT_SOURCE_DIR}/engine/engine-rpc.c
        ${CMAKE_CURRENT_SOURCE_DIR}/engine/engine-rpc-common.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-thread.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-stats.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/proc-stat.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/kprintf.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/precise-time.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/_cpuid.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/server-functions.c
        ${CMAKE_CURRENT_SOURCE_DIR}/common/crc32.c
        ${CMAKE_CURRENT_SOURCE_DIR}/net/net-tcp-rpc-ext-server.c
        ${CMAKE_CURRENT_SOURCE_DIR}/mtproto/mtproto-proxy.c
        ${CMAKE_CURRENT_SOURCE_DIR}/mtproto/mtproto-config.c
       )

target_link_libraries(mtproto-proxy2 rt z m pthread crypto)
target_compile_definitions(mtproto-proxy2 PRIVATE _GNU_SOURCE=1 COMMIT="${GIT_HASH}" _FILE_OFFSET_BITS=64)
target_include_directories(mtproto-proxy2 PRIVATE ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/common)
set_target_properties(mtproto-proxy2 PROPERTIES C_STANDARD 99 CXX_STANDARD 11)
