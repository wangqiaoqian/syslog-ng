set(AFSOCKET_SOURCES
    afsocket.c
    afsocket.h
    afsocket-source.c
    afsocket-source.h
    afsocket-dest.c
    afsocket-dest.h
    socket-options.c
    socket-options.h
    transport-mapper.c
    transport-mapper.h
    afinet.c
    afinet.h
    afinet-source.c
    afinet-source.h
    afinet-dest-failover.c
    afinet-dest-failover.h
    afinet-dest.c
    afinet-dest.h
    socket-options-inet.c
    socket-options-inet.h
    transport-mapper-inet.c
    transport-mapper-inet.h
    afunix-source.c
    afunix-source.h
    afunix-dest.c
    afunix-dest.h
    transport-mapper-unix.c
    transport-mapper-unix.h
    transport-unix-socket.c
    transport-unix-socket.h
    unix-credentials.c
    unix-credentials.h
    afsocket-grammar.y
    afsocket-parser.c
    afsocket-parser.h
    afsocket-plugin.c
    systemd-syslog-source.h
    systemd-syslog-source.c
    afsocket-systemd-override.h
    ${CMAKE_CURRENT_BINARY_DIR}/afsocket-grammar.c
    ${CMAKE_CURRENT_BINARY_DIR}/afsocket-grammar.h
)

generate_y_from_ym(modules/afsocket/afsocket-grammar)

bison_target(AFSocketGrammar
    ${CMAKE_CURRENT_BINARY_DIR}/afsocket-grammar.y
    ${CMAKE_CURRENT_BINARY_DIR}/afsocket-grammar.c
    COMPILE_FLAGS ${BISON_FLAGS})

find_package(ZLIB REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(systemd)

add_library(afsocket SHARED ${AFSOCKET_SOURCES})
target_include_directories(afsocket
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}
)
target_link_libraries(afsocket PRIVATE ${WRAP_LIBRARIES} ${ZLIB_LIBRARIES} ${OPENSSL_LIBRARIES} ${LIBNET_LIBRARIES} ${Libsystemd_LIBRARIES})
target_link_libraries(afsocket PRIVATE syslog-ng)
target_include_directories(afsocket PRIVATE SYSTEM ${ZLIB_INCLUDE_DIRS})
target_include_directories(afsocket PRIVATE SYSTEM ${WRAP_INCLUDE_DIRS})
target_include_directories(afsocket PRIVATE SYSTEM ${Libsystemd_INCLUDE_DIRS})
target_compile_definitions(afsocket PRIVATE ${LIBNET_DEFINES})

install(TARGETS afsocket LIBRARY DESTINATION lib/syslog-ng/ COMPONENT afsocket)

add_test_subdirectory(tests)
