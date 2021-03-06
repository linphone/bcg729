############################################################################
# CMakeLists.txt
# Copyright (C) 2014  Belledonne Communications, Grenoble France
#
############################################################################
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either version 2
# of the License, or (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
#
############################################################################

cmake_minimum_required(VERSION 3.0)
project(BCG729 C)


set(PACKAGE "bcg729")
set(PACKAGE_NAME "${PACKAGE}")
set(PACKAGE_VERSION "1.0.0")
set(PACKAGE_STRING "${PACKAGE_NAME} ${PACKAGE_VERSION}")
set(PACKAGE_BUGREPORT "support@belledonne-communications.com")
set(PACKAGE_TARNAME "bcg729")
set(PACKAGE_URL "")
set(VERSION "${PACKAGE_VERSION}")


option(ENABLE_TESTS "Enable compilation of the tests." No)
option(ENABLE_MSPLUGIN "Enable compilation of the mediastreamer2 plugin." Yes)


if(ENABLE_MSPLUGIN)
	find_package(Mediastreamer2)

	if(NOT MEDIASTREAMER2_FOUND)
		message(WARNING "Could not find the mediastreamer2 libraries!")
		set(ENABLE_MSPLUGIN OFF CACHE BOOL "Enable compilation of the mediastreamer2 plugin." FORCE)
	endif()
endif()


configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h)


include_directories(
	include
	src
	${CMAKE_CURRENT_BINARY_DIR}
	${CMAKE_CURRENT_BINARY_DIR}/src
)
set(MSVC_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include/MSVC")
if(MSVC)
	include_directories(${MSVC_INCLUDE_DIR})
endif()

add_definitions(-DHAVE_CONFIG_H)
if(MSVC)
	add_definitions("/W3")
else()
	add_definitions("-Wall" "-Werror")
endif()

add_subdirectory(src)
if(ENABLE_MSPLUGIN)
	add_subdirectory(msbcg729)
endif()
if(ENABLE_TESTS)
	add_subdirectory(test)
endif()


include(CMakePackageConfigHelpers)
write_basic_package_version_file(
	"${CMAKE_CURRENT_BINARY_DIR}/Bcg729ConfigVersion.cmake"
	VERSION ${PACKAGE_VERSION}
	COMPATIBILITY AnyNewerVersion
)
export(EXPORT Bcg729Targets
	FILE "${CMAKE_CURRENT_BINARY_DIR}/Bcg729Targets.cmake"
	NAMESPACE BelledonneCommunications::
)
configure_file(Bcg729Config.cmake
	"${CMAKE_CURRENT_BINARY_DIR}/Bcg729Config.cmake"
	COPYONLY
)

set(ConfigPackageLocation lib/cmake/Bcg729)
install(EXPORT Bcg729Targets
	FILE Bcg729Targets.cmake
	NAMESPACE BelledonneCommunications::
	DESTINATION ${ConfigPackageLocation}
)
install(FILES
	"${CMAKE_CURRENT_BINARY_DIR}/Bcg729Config.cmake"
	"${CMAKE_CURRENT_BINARY_DIR}/Bcg729ConfigVersion.cmake"
	DESTINATION ${ConfigPackageLocation}
)
