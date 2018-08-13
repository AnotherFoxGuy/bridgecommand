set(BINNAME "bridgecommand-bc")

project(${BINNAME})

file(GLOB sources "*.cpp")
file(GLOB headers "*.hpp")


add_definitions(-DASIO_STANDALONE -DASIO_HAS_STD_THREAD -D_WIN32_WINNT=0x0501)
include_directories(${IRRLICHT_INC_DIR} ${deps_dir}/asio/include ${deps_dir}/serial ${enet_INCLUDE_DIR} ${libsndfile_INCLUDE_DIR} ${portaudio_INCLUDE_DIR})
add_executable(${BINNAME} ${sources} ${headers} ${icon_rc})
target_link_libraries(${BINNAME} PRIVATE Irrlicht ${IRRLICHT_LINK_LIBS} enet serial)
add_dependencies(${BINNAME} libsndfile-pr portaudio-pr)

if (MSVC)
    target_link_libraries(${BINNAME} PRIVATE Winmm)
else ()
    target_link_libraries(${BINNAME} PRIVATE OpenGL::GL)
endif ()

if(BUILD_WITH_SOUND)
    if (MSVC)
        target_link_libraries(${BINNAME} PRIVATE
                "${CMAKE_BINARY_DIR}/dl-dependencies/Build/portaudio-pr/${CMAKE_BUILD_TYPE}/portaudio_x86.lib"
                "${CMAKE_BINARY_DIR}/dl-dependencies/Build/libsndfile-pr/${CMAKE_BUILD_TYPE}/libsndfile-1.lib")
    else ()
        target_link_libraries(${BINNAME} PRIVATE
                "${CMAKE_BINARY_DIR}/dl-dependencies/Build/libsndfile-pr/libsndfile.so"
                "${CMAKE_BINARY_DIR}/dl-dependencies/Build/portaudio-pr/libportaudio.so")
    endif ()
endif ()


