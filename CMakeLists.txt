cmake_minimum_required(VERSION 3.14.0)
project(genet-driver VERSION 1.2.0)

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

add_link_options(-m68040 -mhard-float -s -nostdlib -nostartfiles -lamiga)
add_compile_definitions(PRIVATE VERSION_STRING="${VERSTRING}")

enable_language(ASM_VASM)

add_executable(genet.device
    device.c
    device_beginio.c
    device_abortio.c
    devtree.c
    unit.c
    unit_task.c
    unit_commands.c
    unit_commands_mcast.c
    unit_io.c
    genet/bcmgenet.c
    genet/bcmgenet-tx.c
    genet/bcm_gpio.c
    genet/phy.c
    genet/phy_interface.c
    device_end.c
)

target_link_libraries(genet.device devicetree)
target_include_directories(genet.device PRIVATE "${PROJECT_SOURCE_DIR}/include")
target_compile_options(genet.device PUBLIC $<$<COMPILE_LANGUAGE:C>:-m68040 -mhard-float -O2 -MMD -MP -Wall>)
target_compile_options(genet.device PRIVATE $<$<COMPILE_LANGUAGE:ASM_VASM>:-m68040 -quiet -I${CMAKE_SYSROOT}/m68k-amigaos/ndk-include>)

install(PROGRAMS "${CMAKE_CURRENT_BINARY_DIR}/genet.device" DESTINATION "${CONTRIB_PREFIX}genet-driver/")
install(FILES "README.md" DESTINATION "${CONTRIB_PREFIX}genet-driver/")