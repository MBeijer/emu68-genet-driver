cmake_minimum_required(VERSION 3.14.0)
project(genet-driver VERSION 1.0.0)
get_verstring(VERSTRING)
set_emu68_tools_policies()

add_link_options(-noixemul -m68040 -mhard-float)
add_compile_definitions(PRIVATE VERSION_STRING="${VERSTRING}")

enable_language(ASM_VASM)

file(GLOB_RECURSE SOURCE *.c)

add_executable(genet.device ${SOURCE})

target_link_libraries(genet.device devicetree)

target_compile_options(genet.device PUBLIC $<$<COMPILE_LANGUAGE:C>:-noixemul -m68040 -mhard-float -O2 -MMD -MP -Wall>)
target_compile_options(genet.device PRIVATE $<$<COMPILE_LANGUAGE:ASM_VASM>:-m68040 -quiet -I${CMAKE_SYSROOT}/m68k-amigaos/ndk-include>)

install(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/genet.device DESTINATION ./${CONTRIB_PREFIX}/genet-driver/)
install(FILES README.md DESTINATION ./${CONTRIB_PREFIX}/genet-driver/)
#install(FILES WaitUntilConnected.info DESTINATION ./WaitUntilConnected/)
