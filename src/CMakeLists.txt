############################################################################################################
#                                                                                                          #
#    This file is part of Minor Planet Mayhem                                                              #
#                                                                                                          #
#    Copyright (c) 2014 Shachar Avni. All rights reserved.                                                 #
#                                                                                                          #
#    Use of this file is governed by a BSD-style license. See the accompanying LICENSE.txt for details     #
#                                                                                                          #
############################################################################################################

cmake_minimum_required(VERSION 2.8)

option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
option(MPM_USE_ARCHIVED_RESOURCES "Use MPM resources in an archive" OFF)

find_package(OpenGL REQUIRED)

if (MSVC)
    option(USE_MSVC_RUNTIME_LIBRARY_DLL "Use MSVC runtime library DLL" OFF)
endif()

if(BUILD_SHARED_LIBS)
	add_definitions(-DLOCUS_DLL)
endif()

add_definitions(-DGLEW_MX)
add_definitions(-DGLEW_NO_GLU)

if (NOT BUILD_SHARED_LIBS)
	add_definitions(-DGLEW_STATIC)
endif()

if (MPM_USE_ARCHIVED_RESOURCES)
	add_definitions(-DMPM_USE_ARCHIVE)
endif()

#--------------------------------------------------------------------
# Set compiler specific flags
#--------------------------------------------------------------------
if (UNIX)
    add_definitions(-Wall)
    add_definitions(-std=c++11)

    if (BUILD_SHARED_LIBS)
        add_definitions(-fvisibility=hidden)
    endif()
endif()

set(LOCUS_DIR "../Locus-Game-Engine")

include(${LOCUS_DIR}/cmake/SetMSVCRuntimeLibrarySettings.cmake)
include(${LOCUS_DIR}/cmake/SetMSVCWarningLevel4.cmake)

set(LOCUS_INCLUDE ${LOCUS_DIR}/include)
set(THIRD_PARTY_DIR ${LOCUS_DIR}/third-party)

include_directories(${LOCUS_INCLUDE}
                    ${THIRD_PARTY_DIR}/GLEW/include
					${THIRD_PARTY_DIR}/RapidXML/include)

add_executable(MPM
			   Asteroid.cpp
			   Asteroid.h
			   CelestialObject.cpp
			   CelestialObject.h
			   CollidableTypes.h
			   Config.cpp
			   Config.h
			   DemoScene.cpp
			   DemoScene.h
			   HUD.cpp
			   HUD.h
			   Moon.cpp
			   Moon.h
			   MPM.cpp
			   PauseScene.cpp
			   PauseScene.h
			   Planet.cpp
			   Planet.h
			   Player.cpp
			   Player.h
			   ShaderNames.h
			   Shot.cpp
			   Shot.h
			   TextureManager.cpp
			   TextureManager.h
			   XML.cpp
			   XML.h)

if(WIN32)
	if(MSVC)
		set_target_properties(MPM PROPERTIES LINK_FLAGS "/SUBSYSTEM:WINDOWS")
	elseif(CMAKE_COMPILER_IS_GNUCXX)
		set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mwindows")
	else()
		message("Warning: Unable to set MPM targets subsystem as Windows. MPM may not link due to WinMain entrypoint.")
	endif()
endif()

target_link_libraries(MPM ${OPENGL_LIBRARIES})
target_link_libraries(MPM glew)
target_link_libraries(MPM Locus_Common)
target_link_libraries(MPM Locus_Audio)
target_link_libraries(MPM Locus_FileSystem)
target_link_libraries(MPM Locus_Math)
target_link_libraries(MPM Locus_Geometry)
target_link_libraries(MPM Locus_Rendering)
target_link_libraries(MPM Locus_Simulation)