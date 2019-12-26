set(BINNAME "bridgecommand-bc")

project(${BINNAME})

file(GLOB sources "*.cpp")
file(GLOB headers "*.hpp")


add_definitions(-DASIO_STANDALONE -DASIO_HAS_STD_THREAD -D_WIN32_WINNT=0x0501)
include_directories(${IRRLICHT_INC_DIR} ${deps_dir}/asio/include ${deps_dir}/serial ${enet_INCLUDE_DIR} ${libsndfile_INCLUDE_DIR} ${portaudio_INCLUDE_DIR})
add_executable(${BINNAME} ${sources} ${headers} ${icon_rc})
target_link_libraries(${BINNAME} PRIVATE Irrlicht ${IRRLICHT_LINK_LIBS} enet serial)

if (MSVC)
    target_link_libraries(${BINNAME} PRIVATE Winmm)
	add_definitions(-DNOMINMAX)
else ()
    target_link_libraries(${BINNAME} PRIVATE OpenGL::GL)
endif ()

if(BUILD_WITH_SOUND)
    add_dependencies(${BINNAME} libsndfile-pr portaudio-pr)
    if (MSVC)
        if("${CMAKE_SIZEOF_VOID_P}" STREQUAL "4")
            set(_CL_64 "x86")
        else()
            set(_CL_64 "x64")
        endif()

        target_link_libraries(${BINNAME} PRIVATE "${LIBRARY_OUTPUT_DIRECTORY}/sndfile.lib" "${LIBRARY_OUTPUT_DIRECTORY}/portaudio_${_CL_64}.lib")

		add_custom_command(
                TARGET ${BINNAME}
                POST_BUILD
                COMMAND ${CMAKE_COMMAND} -E copy_if_different ${LIBRARY_OUTPUT_DIRECTORY}/sndfile.dll $<TARGET_FILE_DIR:${BINNAME}>/sndfile.dll
                COMMAND ${CMAKE_COMMAND} -E copy_if_different ${LIBRARY_OUTPUT_DIRECTORY}/portaudio_${_CL_64}.dll $<TARGET_FILE_DIR:${BINNAME}>/portaudio_${_CL_64}.dll
        )

    else ()
        target_link_libraries(${BINNAME} PRIVATE
                "${LIBRARY_OUTPUT_DIRECTORY}/libsndfile.so"
                "${LIBRARY_OUTPUT_DIRECTORY}/libportaudio.so")
    endif ()
endif ()

cotire(${BINNAME})
