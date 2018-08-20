set(BINNAME "bridgecommand-rp")

project(${BINNAME})

set(SOURCE_FILES
        main.cpp
        ${CMAKE_SOURCE_DIR}/source/IniFile.{hpp,cpp}
        ${CMAKE_SOURCE_DIR}/source/Lang.{hpp,cpp}
        ${CMAKE_SOURCE_DIR}/source/Utilities.{hpp,cpp}
        ${CMAKE_SOURCE_DIR}/source/HeadingIndicator.{h,cpp}
        ControllerModel.{hpp,cpp}
        EventReceiver.{hpp,cpp}
        GUI.{hpp,cpp}
        Network.{hpp,cpp}
        )

# Expand file extensions (i.e. path/to/file.{h,cpp} becomes path/to/file.h;path/to/file.cpp)
expand_file_extensions(SOURCE_FILES ${SOURCE_FILES})

add_executable(${BINNAME} ${icon_rc} ${SOURCE_FILES})

add_definitions(-DASIO_STANDALONE -DASIO_HAS_STD_THREAD -D_WIN32_WINNT=0x0501)
include_directories(${CMAKE_SOURCE_DIR}/source ${IRRLICHT_INC_DIR} ${deps_dir}/asio/include ${deps_dir}/serial ${enet_INCLUDE_DIR})
target_link_libraries(${BINNAME} PRIVATE Irrlicht ${IRRLICHT_LINK_LIBS} enet serial)

if (MSVC)
    target_link_libraries(${BINNAME} PRIVATE Winmm Ws2_32)
else ()
    target_link_libraries(${BINNAME} PRIVATE OpenGL::GL)
endif ()

cotire(${BINNAME})