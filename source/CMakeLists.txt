set(BINNAME "bridgecommand-bc")

project(${BINNAME})

file(GLOB sources "*.cpp")
file(GLOB headers "*.hpp")

add_definitions(-DASIO_STANDALONE -DASIO_HAS_STD_THREAD -D_WIN32_WINNT=0x0501)
include_directories(${IRRLICHT_INC_DIR} ${deps_dir}/asio/include ${deps_dir}/serial ${enet_INCLUDE_DIR})
add_executable(${BINNAME} ${sources} ${headers} ${icon_rc})
target_link_libraries(${BINNAME} PRIVATE Irrlicht ${IRRLICHT_LINK_LIBS} enet serial)

if (MSVC)
    target_link_libraries(${BINNAME} PRIVATE Winmm)
else ()
    target_link_libraries(${BINNAME} PRIVATE OpenGL::GL)
endif ()
