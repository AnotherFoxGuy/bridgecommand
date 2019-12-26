cmake_minimum_required(VERSION 3.0.2)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/CMake)
set(deps_dir ${CMAKE_SOURCE_DIR}/dependencies)

include(SourceFileUtils)
include(FeatureSummary)
include(ExternalProject)

# Download cotire automatically
if(NOT EXISTS "${CMAKE_BINARY_DIR}/cotire.cmake")
   message(STATUS "Downloading cotire.cmake from https://github.com/sakra/cotire/")
   file(DOWNLOAD "https://raw.githubusercontent.com/sakra/cotire/master/CMake/cotire.cmake"
                 "${CMAKE_BINARY_DIR}/cotire.cmake")
endif()

include(${CMAKE_BINARY_DIR}/cotire.cmake)

project(bridgecommand)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(CMAKE_CXX_STANDARD 11)

find_package(OpenGL)

option(BUILD_WITH_SOUND "Build with sound" ON)
if (MSVC)
    set(icon_rc "${CMAKE_SOURCE_DIR}/resources/icon.rc")
endif ()

# setup paths
SET(RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/")
SET(LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib/")
SET(ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib/")
SET(EXECUTABLE_OUTPUT_PATH ${RUNTIME_OUTPUT_DIRECTORY})
SET(LIBRARY_OUTPUT_PATH ${RUNTIME_OUTPUT_DIRECTORY})
# Set the EP_BASE directory property to setup the build directory structure (see the ExternalProject documentation for more information)
set_property(DIRECTORY PROPERTY EP_BASE ${CMAKE_BINARY_DIR}/external-dependencies)

add_subdirectory(${deps_dir}/Irrlicht/irrlicht-svn)
add_subdirectory(${deps_dir}/enet)
add_subdirectory(${deps_dir}/serial)


if (BUILD_WITH_SOUND)
    add_definitions(-DWITH_SOUND)

    set(libsndfile_INCLUDE_DIR "${CMAKE_BINARY_DIR}/external-dependencies/Build/libsndfile-pr/src")
    set(portaudio_INCLUDE_DIR "${CMAKE_BINARY_DIR}/external-dependencies/Source/portaudio-pr/include")

    ExternalProject_Add(
            libsndfile-pr
            GIT_REPOSITORY https://github.com/erikd/libsndfile.git
            GIT_SHALLOW TRUE
            GIT_PROGRESS TRUE
            CMAKE_ARGS
            -DBUILD_EXAMPLES=OFF
            -DBUILD_PROGRAMS=OFF
            -DBUILD_TESTING=OFF
            -DBUILD_SHARED_LIBS=ON
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY_RELWITHDEBINFO=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELWITHDEBINFO=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
            -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
            INSTALL_COMMAND ""
    )

    ExternalProject_Add(
            portaudio-pr
            GIT_REPOSITORY https://git.assembla.com/portaudio.git
            GIT_SHALLOW TRUE
            GIT_PROGRESS TRUE
            CMAKE_ARGS
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_LIBRARY_OUTPUT_DIRECTORY_RELWITHDEBINFO=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELWITHDEBINFO=${LIBRARY_OUTPUT_DIRECTORY}
            -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
            -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
            INSTALL_COMMAND ""
    )
endif ()

add_subdirectory(controller)
add_subdirectory(editor)
add_subdirectory(iniEditor)
add_subdirectory(launcher)
add_subdirectory(multiplayerHub)
add_subdirectory(repeater)
add_subdirectory(source)


set(cp_dir $<TARGET_FILE_DIR:bridgecommand>)
add_custom_target(
        copy_resources
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/bc5.ini ${cp_dir}/bc5.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/map.ini ${cp_dir}/map.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/mph.ini ${cp_dir}/mph.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/repeater.ini ${cp_dir}/repeater.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/language-en.txt ${cp_dir}/language-en.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/languageController-en.txt ${cp_dir}/languageController-en.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/languageMultiplayer-en.txt ${cp_dir}/languageMultiplayer-en.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/languageLauncher-en.txt ${cp_dir}/languageLauncher-en.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/languageIniEditor-en.txt ${cp_dir}/languageIniEditor-en.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/resources/languageRepeater-en.txt ${cp_dir}/languageRepeater-en.txt
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/content/ ${cp_dir}/
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/doc/ ${cp_dir}/doc/
        COMMENT "Copy resources to build directory"
        VERBATIM
)


feature_summary(WHAT ALL)
