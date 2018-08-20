set(BINNAME "bridgecommand-ini")

project(${BINNAME})
add_executable(${BINNAME}
        main.cpp
        ${icon_rc}
        ${CMAKE_SOURCE_DIR}/source/IniFile.cpp
        ${CMAKE_SOURCE_DIR}/source/Lang.cpp
        ${CMAKE_SOURCE_DIR}/source/Utilities.cpp
        )

include_directories(${IRRLICHT_INC_DIR} ${CMAKE_SOURCE_DIR}/source)
target_link_libraries(${BINNAME} PRIVATE Irrlicht ${IRRLICHT_LINK_LIBS})

if (NOT MSVC)
    target_link_libraries(${BINNAME} PRIVATE OpenGL::GL)
endif ()

cotire(${BINNAME})