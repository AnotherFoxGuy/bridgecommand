cmake_minimum_required(VERSION 3.0.2)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/CMake)
set(deps_dir ${CMAKE_SOURCE_DIR}/dependencies)

include(SourceFileUtils)
include(FeatureSummary)

project(bridgecommand)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(CMAKE_CXX_STANDARD 11)
find_package(OpenGL)

if (MSVC)
    set(icon_rc "${CMAKE_SOURCE_DIR}/etc/icon.rc")
endif ()

# setup paths
SET(RUNTIME_OUTPUT_DIRECTORY "${bridgecommand_BINARY_DIR}/bin/")
SET(LIBRARY_OUTPUT_DIRECTORY "${bridgecommand_BINARY_DIR}/lib/")
SET(ARCHIVE_OUTPUT_DIRECTORY "${bridgecommand_BINARY_DIR}/lib/")
SET(EXECUTABLE_OUTPUT_PATH ${RUNTIME_OUTPUT_DIRECTORY})
SET(LIBRARY_OUTPUT_PATH ${RUNTIME_OUTPUT_DIRECTORY})

add_subdirectory(${deps_dir}/Irrlicht/irrlicht-svn)
add_subdirectory(${deps_dir}/enet)
add_subdirectory(${deps_dir}/serial)

add_subdirectory(controller)
add_subdirectory(editor)
add_subdirectory(iniEditor)
add_subdirectory(launcher)
add_subdirectory(multiplayerHub)
add_subdirectory(repeater)
add_subdirectory(source)


set(cp_dir $<TARGET_FILE_DIR:bridgecommand>)
add_custom_target(
        copy-resources
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/bc5.ini ${cp_dir}/bc5.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/map.ini ${cp_dir}/map.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/mph.ini ${cp_dir}/mph.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/repeater.ini ${cp_dir}/repeater.ini
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/language.txt ${cp_dir}/language.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/languageController.txt ${cp_dir}/languageController.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/languageMultiplayer.txt ${cp_dir}/languageMultiplayer.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/languageLauncher.txt ${cp_dir}/languageLauncher.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/languageIniEditor.txt ${cp_dir}/languageIniEditor.txt
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_SOURCE_DIR}/etc/languageRepeater.txt ${cp_dir}/languageRepeater.txt
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/resources/ ${cp_dir}/
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/doc/ ${cp_dir}/doc/
        COMMENT "Copy resources to build directory"
        VERBATIM
)


feature_summary(WHAT ALL)
