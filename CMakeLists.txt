cmake_minimum_required(VERSION 3.20)
project(zypp-stack)

include(FetchContent)

function ( enableOption opt )
  set ( ${opt} TRUE CACHE BOOL "${enableOption} enabled via zypp-stack" FORCE )
endfunction()

function ( disableOption opt )
  set ( ${opt} FALSE CACHE BOOL "${enableOption} disabled via zypp-stack" FORCE )
endfunction()

# This option will reroute all tool binaries to the libzypp build dir instead of taking those installed in the default directories.
OPTION (ENABLE_DEVEL_BUILD "Developer build, use zypp and solv binaries directly from build dir rather than the default locations" ON)

# tell the subproject they are part of a stack build
set( ZYPP_STACK_BUILD TRUE )

# libsolv build options, these are the default settings on building for SUSE
# distributions
enableOption ( SUSE )
enableOption ( CMAKE_VERBOSE_MAKEFILE )
enableOption ( WITH_LIBXML2 )
enableOption ( MULTI_SEMANTICS )
enableOption ( ENABLE_STATIC )
enableOption ( DISABLE_SHARED )
enableOption ( ENABLE_APPDATA )
enableOption ( ENABLE_COMPS )
enableOption ( ENABLE_BZIP2_COMPRESSION )
enableOption ( ENABLE_LZMA_COMPRESSION )
enableOption ( ENABLE_ZSTD_COMPRESSION )
enableOption ( ENABLE_ZCHUNK_COMPRESSION )
enableOption ( ENABLE_COMPLEX_DEPS )
enableOption ( ENABLE_SUSEREPO )
enableOption ( ENABLE_HELIXREPO )
enableOption ( USE_VENDORDIRS )

add_subdirectory( libsolv )

#prepare include tree for libzypp, libsolv project and install directory structure is too different otherwise
file( MAKE_DIRECTORY ${PROJECT_BINARY_DIR}/include/solv )

file( GLOB files "${PROJECT_SOURCE_DIR}/libsolv/src/*.h" )
file( GLOB files_ext "${PROJECT_SOURCE_DIR}/libsolv/ext/*.h" )
foreach(file ${files} ${files_ext})
  cmake_path(GET file FILENAME _file_name )
  file( CREATE_LINK ${file} "${PROJECT_BINARY_DIR}/include/solv/${_file_name}" SYMBOLIC )
endforeach()

file( CREATE_LINK ${PROJECT_BINARY_DIR}/libsolv/src/solvversion.h ${PROJECT_BINARY_DIR}/include/solv/solvversion.h SYMBOLIC )
set( ZYPP_STACK_INCLUDE_DIRS ${PROJECT_BINARY_DIR}/include )

#libzypp build options
enableOption  ( ENABLE_DEVEL_BUILD )
enableOption  ( ENABLE_BUILD_TESTS )
enableOption  ( ENABLE_BUILD_TRANS )
enableOption  ( ENABLE_VISIBILITY_HIDDEN )
enableOption  ( INSTALL_NG_BINARIES )
disableOption  ( ENABLE_UBSAN_CHECKS )
enableOption  ( ENABLE_ZCHUNK_COMPRESSION )
disableOption ( DISABLE_MEDIABACKEND_TESTS )

enableOption  ( ENABLE_LIBZYPPNG_BUILD )
disableOption  ( ENABLE_DEVTOOL_REFRESHNG )

add_subdirectory( libzypp )

file( MAKE_DIRECTORY ${PROJECT_BINARY_DIR}/include/zypp-core )
file( CREATE_LINK ${PROJECT_BINARY_DIR}/libzypp/zypp/zypp-logic/zypp-core/APIConfig.h ${PROJECT_BINARY_DIR}/include/zypp-core/APIConfig.h SYMBOLIC )

add_subdirectory( zypper )

add_subdirectory( zypp-solver-testcase-runner )

#FetchContent_Declare(
#  solv
#  SOURCE_DIR    ${PROJECT_SOURCE_DIR}/libsolv
#  BINARY_DIR    ${PROJECT_BINARY_DIR}/libsolv
#  PATCH_COMMAND git apply ${PROJECT_SOURCE_DIR}/libsolv.patch
#)

#FetchContent_Declare(
#  zypp
#  SOURCE_DIR    ${PROJECT_SOURCE_DIR}/libzypp
#  BINARY_DIR    ${PROJECT_BINARY_DIR}/libzypp
#  PATCH_COMMAND git apply ${PROJECT_SOURCE_DIR}/libzypp.patch
#)

#FetchContent_MakeAvailable( solv zyppa )
