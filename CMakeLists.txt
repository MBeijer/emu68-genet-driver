cmake_minimum_required(VERSION 3.14.0)
project(emu68-genet-driver VERSION 1.4)


# CPM BEGIN
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")

include(CPM)

set(CPM_SOURCE_CACHE "${PROJECT_SOURCE_DIR}/external")
CPMUsePackageLock(package-lock.cmake)

CPMGetPackage(CPMLicenses.cmake)
CPMGetPackage(CMakeAmigaCommon)

if(NOT TARGET write-licenses)
	cpm_licenses_create_disclaimer_target(
		write-licenses "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/third_party.txt" "${CPM_PACKAGES}"
	)
endif()
# CPM END

get_verstring(VERSTRING)

# Set default install prefix to ./install subdirectory
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "${CMAKE_SOURCE_DIR}/install" CACHE PATH "Install path" FORCE)
endif()

add_subdirectory(common)
add_subdirectory(runtime-config)
add_subdirectory(genet.device)

# install(FILES ${CMAKE_SOURCE_DIR}/Install DESTINATION .)
# install(FILES ${CMAKE_SOURCE_DIR}/Install.info DESTINATION .)
