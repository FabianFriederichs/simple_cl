cmake_minimum_required(VERSION 3.1)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
include(package_helpers)

set(lib_name simple_cl)

project(${lib_name} VERSION 1.0.0 LANGUAGES C CXX)

# depends on OpenCL but only the basic cl.h header. No fancy bindings needed.
find_package(OpenCL REQUIRED) # creates target OpenCL::OpenCL

add_library(${lib_name} STATIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include/simple_cl.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/include/simple_cl_error.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/simple_cl.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/simple_cl_error.cpp
)

target_link_libraries(${lib_name}
    PUBLIC
        OpenCL::OpenCL
)

target_compile_definitions(${lib_name}
	PUBLIC
		$<$<CONFIG:Debug>:CLERR_DEBUG>
        CL_TARGET_OPENCL_VERSION=120 # restrict outselves to OpenCL 1.2 as it is available virtually everywhere
)

target_compile_features(${lib_name} PUBLIC cxx_std_11)

target_include_directories(${lib_name}
    INTERFACE 
        $<INSTALL_INTERFACE:include>    
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

set_target_properties(${lib_name}
    PROPERTIES
        DEBUG_POSTFIX "d"
)

# creates package files and does all the install stuff
make_package(NAME ${lib_name}
    TARGETS
        ${lib_name}
    PUBLIC_INCLUDE_DIRECTORIES
        "${CMAKE_CURRENT_SOURCE_DIR}/include"
    RUNTIME_DESTINATION bin
    LIBRARY_DESTINATION lib
    ARCHIVE_DESTINATION lib
    INCLUDE_DESTINATION .
    CONFIG_DESTINATION lib/cmake
    VERSION_MAJOR ${PROJECT_VERSION_MAJOR}
    VERSION_MINOR ${PROJECT_VERSION_MINOR}
    VERSION_PATCH ${PROJECT_VERSION_PATCH}
    COMPATIBILITY SameMajorVersion
    DEPENDENCIES
        OpenCL
)

# # install target
# install(TARGETS ${lib_name}
#         EXPORT "${lib_name}Targets"
#         RUNTIME DESTINATION bin
#         LIBRARY DESTINATION lib
#         ARCHIVE DESTINATION lib
# )

# # export targets
# install(EXPORT "${lib_name}Targets" FILE "${lib_name}Targets.cmake" DESTINATION lib/cmake)

# # create package
# include(CMakePackageConfigHelpers)
# configure_package_config_file("${CMAKE_CURRENT_SOURCE_DIR}/${lib_name}Config.cmake.in"
#   "${CMAKE_CURRENT_BINARY_DIR}/${lib_name}Config.cmake"
#   INSTALL_DESTINATION lib/cmake
# )

# # versioning
# set_target_properties(${lib_name}
#     PROPERTIES
#         VERSION ${version}
#         SOVERSION ${version_major}
#         INTERFACE_${lib_name}_MAJOR_VERSION ${version_major}
#         COMPATIBLE_INTERFACE_STRING ${lib_name}_MAJOR_VERSION
# )
# write_basic_package_version_file(
#     "${CMAKE_CURRENT_BINARY_DIR}/"
# )



# install(DIRECTORY include DESTINATION .)

