#================================================================
# generate zlog lib
#================================================================
aux_source_directory(. SRCS)
if (NOT WIN32)
    list(REMOVE_ITEM SRCS ./zlog_win.c)
endif()

set(zlog_TARGETS "")

list(REMOVE_ITEM SRCS ./zlog-chk-conf.c)

if (ZLOG_BUILD_SHARED_LIBS)
    add_library(zlog
        SHARED
        ${SRCS}
        )
    target_link_libraries(zlog
        ${CMAKE_THREAD_PREFER_PTHREAD}
        )

    if (WIN32)
        target_link_libraries(zlog
            ${UNIXEM_LIBRARY}
            Ws2_32
            )
    endif()

    set_target_properties(zlog PROPERTIES VERSION ${zlog_ver} SOVERSION ${zlog_so_ver})

    list(APPEND zlog_TARGETS zlog)
endif()

if (ZLOG_BUILD_STATIC_LIBS)
    add_library(zlog_s
        STATIC
        ${SRCS}
        )
    target_link_libraries(zlog_s
        ${CMAKE_THREAD_PREFER_PTHREAD}
        )

    if (WIN32)
        target_link_libraries(zlog_s
            ${UNIXEM_LIBRARY}
            Ws2_32
            )
    endif()

    set_target_properties(zlog_s PROPERTIES OUTPUT_NAME zlog)

    list(APPEND zlog_TARGETS zlog_s)
endif()

#================================================================
# generate zlog-chk-conf
#================================================================
if (ZLOG_BUILD_CONF_CHK)
    add_executable(zlog-chk-conf zlog-chk-conf.c)
    target_link_libraries(zlog-chk-conf zlog)

    list(APPEND zlog_TARGETS zlog-chk-conf)
endif()

#================================================================
# install
#================================================================
install(TARGETS
    ${zlog_TARGETS}
    COMPONENT zlog
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
    )

install(FILES
    zlog.h
    COMPONENT zlog
    DESTINATION include
    )
