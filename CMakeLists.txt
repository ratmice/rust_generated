cmake_minimum_required(VERSION 3.12)
find_package(Corrosion REQUIRED CMAKE_FIND_ROOT_PATH_BOTH)

add_custom_command(
	OUTPUT gen_headers/arch/api/invocation.h
	DEPENDS $<TARGET_FILE:gen_invocations>
	COMMAND $<TARGET_FILE:gen_invocations> --lang c --xml ${CMAKE_CURRENT_SOURCE_DIR}/../../libsel4/arch_include/${KernelArch}/interfaces/sel4arch.xml --arch --dest gen_headers/arch/api/invocation.h
)

add_custom_command(
	OUTPUT gen_headers/arch/api/sel4_invocation.h 
	DEPENDS $<TARGET_FILE:gen_invocations>
	COMMAND $<TARGET_FILE:gen_invocations> --lang c --xml ${CMAKE_CURRENT_SOURCE_DIR}/../../libsel4/sel4_arch_include/${KernelSel4Arch}/interfaces/sel4arch.xml --sel4-arch --dest gen_headers/arch/api/sel4_invocation.h
)

add_custom_command(
        OUTPUT gen_headers/api/invocation.h
        DEPENDS $<TARGET_FILE:gen_invocations>
        COMMAND $<TARGET_FILE:gen_invocations> --lang c --xml ${CMAKE_CURRENT_SOURCE_DIR}/../../libsel4/include/interfaces/sel4.xml --dest gen_headers/api/invocation.h
)

add_custom_command(
	OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/arch/api/invocation.rs
        DEPENDS $<TARGET_FILE:gen_invocations>
        COMMAND $<TARGET_FILE:gen_invocations> --lang rust --xml ${CMAKE_CURRENT_SOURCE_DIR}/../../libsel4/arch_include/${KernelArch}/interfaces/sel4arch.xml --arch --dest ${CMAKE_CURRENT_SOURCE_DIR}/src/arch/api/invocation.rs
)

add_custom_command(
        OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/arch/api/sel4_invocation.rs
        DEPENDS $<TARGET_FILE:gen_invocations>
        COMMAND $<TARGET_FILE:gen_invocations> --lang rust --xml ${CMAKE_CURRENT_SOURCE_DIR}/../../libsel4/sel4_arch_include/${KernelSel4Arch}/interfaces/sel4arch.xml --sel4-arch --dest ${CMAKE_CURRENT_SOURCE_DIR}/src/arch/api/sel4_invocation.rs
)

add_custom_command(
        OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/api/invocation.rs
        DEPENDS $<TARGET_FILE:gen_invocations>
        COMMAND $<TARGET_FILE:gen_invocations> --lang rust --xml ${CMAKE_CURRENT_SOURCE_DIR}/../../libsel4/include/interfaces/sel4.xml --dest ${CMAKE_CURRENT_SOURCE_DIR}/src/api/invocation.rs
)

file(MAKE_DIRECTORY gen_headers/arch/api)
file(MAKE_DIRECTORY gen_headers/api)
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/arch/api)
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/api)

add_custom_target(generated_sources
   DEPENDS
     gen_headers/arch/api/invocation.h
     gen_headers/arch/api/sel4_invocation.h
     gen_headers/api/invocation.h
     ${CMAKE_CURRENT_SOURCE_DIR}/arch/api/invocation.rs
     ${CMAKE_CURRENT_SOURCE_DIR}/arch/api/sel4_invocation.rs
     ${CMAKE_CURRENT_SOURCE_DIR}/api/invocation.rs
)

corrosion_import_crate(MANIFEST_PATH ${CMAKE_CURRENT_SOURCE_DIR}/Cargo.toml)
add_dependencies(cargo-build_libfoo generated_sources)
