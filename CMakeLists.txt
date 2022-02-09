cmake_minimum_required(VERSION 3.12)
find_package(Corrosion REQUIRED CMAKE_FIND_ROOT_PATH_BOTH)

add_custom_command(
	OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/src/foo.rs
	DEPENDS $<TARGET_PROPERTY:gen_invocations,IMPORTED_LOCATION_DEBUG>
	COMMAND $<TARGET_PROPERTY:gen_invocations,IMPORTED_LOCATION_DEBUG> --lang rust --xml foo.xml --header header.jinja --arch x86_64 -o ${CMAKE_CURRENT_SOURCE_DIR}/src/foo.rs
)

add_custom_target(generated_sources DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/foo.rs)

corrosion_import_crate(MANIFEST_PATH ${CMAKE_CURRENT_SOURCE_DIR}/Cargo.toml)
add_dependencies(cargo-build_libfoo generated_sources)
