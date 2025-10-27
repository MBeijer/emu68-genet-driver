cmake_minimum_required(VERSION 3.14.0)
project(genet.device VERSION ${CMAKE_PROJECT_VERSION})

get_verstring(VERSTRING)

if(NOT TARGET devicetree)
    CPMGetPackage(devicetree.resource)
endif()

add_executable(genet.device
    src/device.c
    src/device_beginio.c
    src/device_abortio.c
    src/devtree_parse.c
    src/unit.c
    src/unit_task.c
    src/unit_commands.c
    src/unit_commands_mcast.c
    src/unit_io.c
    src/bcmgenet.c
    src/bcmgenet-tx.c
    src/phy.c
    src/phy_interface.c
    src/device_end.c
)

target_compile_definitions(genet.device PRIVATE DEVICE_IDSTRING="${VERSTRING}" DEVICE_VERSION=${PROJECT_VERSION_MAJOR} DEVICE_REVISION=${PROJECT_VERSION_MINOR} DEVICE_NAME="${PROJECT_NAME}")
add_dependencies(genet.device devicetree)
target_link_libraries(genet.device devicetree runtime-config common)
target_link_options(genet.device PRIVATE -ffreestanding -nostdlib -nostartfiles -s -Wl,-e,_doNotExecute)
target_include_directories(genet.device PRIVATE "${PROJECT_SOURCE_DIR}/include")
target_compile_options(genet.device PUBLIC $<$<COMPILE_LANGUAGE:C>:-Os -m68040 -Wall -Wextra -DDEBUG -fomit-frame-pointer>)
target_compile_options(genet.device PRIVATE $<$<COMPILE_LANGUAGE:ASM_VASM>:-m68040 -quiet -I${CMAKE_SYSROOT}/m68k-amigaos/ndk-include>)

install(PROGRAMS "${CMAKE_CURRENT_BINARY_DIR}/genet.device" DESTINATION .)
install(FILES "README.md" DESTINATION .)