cmake_minimum_required (VERSION 2.8)

set(PROXY_LIB_FILE "${CMAKE_CURRENT_SOURCE_DIR}/target/release/libtiflash_proxy")
if (APPLE)
    set(PROXY_LIB_FILE "${PROXY_LIB_FILE}.dylib")
elseif (WINDOWS)
    set(PROXY_LIB_FILE "${PROXY_LIB_FILE}.dll")
else()
    set(PROXY_LIB_FILE "${PROXY_LIB_FILE}.so")
endif()

file(GLOB_RECURSE PROXY_SOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "src/*.rs")

add_custom_command(
    OUTPUT ${PROXY_LIB_FILE}
    COMMAND make release
    WORKING_DIRECTORY ${ClickHouse_SOURCE_DIR}/contrib/tiflash-proxy/
    DEPENDS ${PROXY_SOURCES}
    COMMENT "bundled building tiflash_proxy..."
)
add_custom_target(PROXY_TARGET
    DEPENDS ${PROXY_LIB_FILE}
)

add_library(PROXY_LIB SHARED IMPORTED GLOBAL)
add_dependencies(PROXY_LIB PROXY_TARGET)
set_target_properties(PROXY_LIB PROPERTIES IMPORTED_LOCATION ${PROXY_LIB_FILE})
