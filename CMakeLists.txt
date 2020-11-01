cmake_minimum_required (VERSION 3.18.2)

set (CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_CURRENT_SOURCE_DIR}/cmake")

project(MiniMeters)

find_package(SDL2 REQUIRED)
find_package(SDL2_image REQUIRED)
find_package(SDL2_ttf REQUIRED)
# find_package(PortAudio REQUIRED)
# find_package(CURL REQUIRED)
# find_package(FFTW REQUIRED)

set(RESOURCE_FILES

)
file(GLOB_RECURSE src "Source/*")
file(GLOB source_files
	${src}
  ${RESOURCE_FILES}
)

file(GLOB FRAMEWORKS
  ${PORTAUDIO_LIBRARIES}
  ${FFTW_FOLLOWED}
)

set(CMAKE_MACOSX_RPATH TRUE)
set(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
list(APPEND CMAKE_INSTALL_RPATH "@executable_path/../Frameworks/")

add_executable(${CMAKE_PROJECT_NAME} ${source_files} ${FRAMEWORKS})

# Maintains Folder Structure
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${source_files} )

set_property(TARGET ${CMAKE_PROJECT_NAME} PROPERTY CXX_STANDARD 17)

include_directories(
                    # ${PORTAUDIO_INCLUDE_DIR}
                    ${SDL2_INCLUDE_DIR}
                    ${SDL2_IMAGE_INCLUDE_DIR}
                    ${SDL2_TTF_INCLUDE_DIR}
                    # SYSTEM ${CURL_INCLUDE_DIRS}
                    # ${FFTW_INCLUDE_DIRS}
                    )

target_link_libraries(${CMAKE_PROJECT_NAME} ${SDL2_LIBRARY}
                      ${SDL2_IMAGE_LIBRARIES}
                      ${SDL2_TTF_LIBRARIES}
      			          # ${PORTAUDIO_LIBRARIES}
                      # CURL
                      # ${FFTW_LIBRARIES}
                      )


set_source_files_properties("/Library/Frameworks/SDL2.framework" PROPERTIES MACOSX_PACKAGE_LOCATION Frameworks)
set_source_files_properties("/Library/Frameworks/SDL2_image.framework" PROPERTIES MACOSX_PACKAGE_LOCATION Frameworks)
set_source_files_properties("/Library/Frameworks/SDL2_ttf.framework" PROPERTIES MACOSX_PACKAGE_LOCATION Frameworks)

# This little macro lets you set any XCode specific property
macro (set_xcode_property TARGET XCODE_PROPERTY XCODE_VALUE)
    set_property (TARGET ${TARGET} PROPERTY XCODE_ATTRIBUTE_${XCODE_PROPERTY} ${XCODE_VALUE})
endmacro (set_xcode_property)

SET_XCODE_PROPERTY(${CMAKE_PROJECT_NAME} CODE_SIGN_IDENTITY "$(CODE_SIGN_IDENTITY_$())")

# this automatically imports libraries into the app bundle.
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND dylibbundler -od -b -x ./${CMAKE_PROJECT_NAME}.app/Contents/MacOS/${CMAKE_PROJECT_NAME} -p ./${CMAKE_PROJECT_NAME}.app/Contents/Frameworks/
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>/
    COMMENT "Updaing dylib paths"
)


set_target_properties(${CMAKE_PROJECT_NAME} PROPERTIES
  MACOSX_BUNDLE TRUE
  MACOSX_BUNDLE_INFO_PLIST "../Source/Info.plist"
  MACOSX_FRAMEWORK_IDENTIFIER com.direct.${CMAKE_PROJECT_NAME}
  RESOURCE "${RESOURCE_FILES}")

macro(print_all_variables)
    message(STATUS "print_all_variables------------------------------------------{")
    get_cmake_property(_variableNames VARIABLES)
    foreach (_variableName ${_variableNames})
        message(STATUS "${_variableName}=${${_variableName}}")
    endforeach()
    message(STATUS "print_all_variables------------------------------------------}")
endmacro()

set(CPACK_GENERATOR "${CMAKE_PROJECT_NAME}")
# print_all_variables()

