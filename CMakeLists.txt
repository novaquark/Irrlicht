#
# Irrlicht 3D engine
#
cmake_minimum_required(VERSION 2.8)
project(IrrlichtEngine)

# Options
# -------------------------------------------------------------------------------------------------
set(IRR_FAST_MATH 0 CACHE BOOL "Whether to enable fast maths (at the expense of precision)")
set(BUILD_SHARED_LIBS 1 CACHE BOOL "Whether to generate shared libraries instead of static libraries")

# Some helper functions
# -------------------------------------------------------------------------------------------------
function(glob_c_cpp_sources result folder)
	file(GLOB res
		"${folder}/*.c"
		"${folder}/*.cpp"
		"${folder}/*.h"
		"${folder}/*.hpp")
	set(${result} ${res} PARENT_SCOPE)
endfunction()

function(setup_target_paths Target LibDir RTDir)
    set_target_properties(${Target} PROPERTIES
        OUTPUT_NAME ${Target}
        DEBUG_POSTFIX _d
        RELWITHDEBINFO_POSTFIX _rd
        MINSIZEREL_POSTFIX _rm
        RUNTIME_OUTPUT_DIRECTORY ${RTDir}
        LIBRARY_OUTPUT_DIRECTORY ${LibDir}
        ARCHIVE_OUTPUT_DIRECTORY ${LibDir}
        PDB_OUTPUT_DIRECTORY ${RTDir}
        RUNTIME_OUTPUT_DIRECTORY_DEBUG ${RTDir}
        LIBRARY_OUTPUT_DIRECTORY_DEBUG ${LibDir}
        ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${LibDir}
        PDB_OUTPUT_DIRECTORY_DEBUG ${RTDir}
        RUNTIME_OUTPUT_DIRECTORY_RELEASE ${RTDir}
        LIBRARY_OUTPUT_DIRECTORY_RELEASE ${LibDir}
        ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${LibDir}
        PDB_OUTPUT_DIRECTORY_RELEASE ${RTDir}
        RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${RTDir}
        LIBRARY_OUTPUT_DIRECTORY_RELWITHDEBINFO ${LibDir}
        ARCHIVE_OUTPUT_DIRECTORY_RELWITHDEBINFO ${LibDir}
        PDB_OUTPUT_DIRECTORY_RELWITHDEBINFO ${RTDir}
        RUNTIME_OUTPUT_DIRECTORY_MINSIZEREL ${RTDir}
        LIBRARY_OUTPUT_DIRECTORY_MINSIZEREL ${LibDir}
        ARCHIVE_OUTPUT_DIRECTORY_MINSIZEREL ${LibDir}
        PDB_OUTPUT_DIRECTORY_MINSIZEREL ${RTDir}
    )
endfunction()

# Irrlicht directories
# -------------------------------------------------------------------------------------------------
set(IRR_ROOT_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set(IRR_SRC_DIR "${IRR_ROOT_DIR}/source/Irrlicht")

# Source files
# -------------------------------------------------------------------------------------------------
glob_c_cpp_sources(IRR_SRC_FILES ${IRR_SRC_DIR})
glob_c_cpp_sources(IRR_SRC_FILES_AESGLADMAN ${IRR_SRC_DIR}/aesGladman)
glob_c_cpp_sources(IRR_SRC_FILES_BZIP2 ${IRR_SRC_DIR}/bzip2)
glob_c_cpp_sources(IRR_SRC_FILES_JPEGLIB ${IRR_SRC_DIR}/jpeglib)
glob_c_cpp_sources(IRR_SRC_FILES_LIBPNG ${IRR_SRC_DIR}/libpng)
glob_c_cpp_sources(IRR_SRC_FILES_LZMA ${IRR_SRC_DIR}/lzma)
glob_c_cpp_sources(IRR_SRC_FILES_ZLIB ${IRR_SRC_DIR}/zlib)

# Remove test/dll main code from (some) thirdparty libs
# -------------------------------------------------------------------------------------------------
list(REMOVE_ITEM IRR_SRC_FILES_BZIP2
	"${IRR_SRC_DIR}/bzip2/bzip2.c"
	"${IRR_SRC_DIR}/bzip2/bzip2recover.c"
	"${IRR_SRC_DIR}/bzip2/dlltest.c"
	"${IRR_SRC_DIR}/bzip2/mk251.c"
	"${IRR_SRC_DIR}/bzip2/spewG.c"
	"${IRR_SRC_DIR}/bzip2/unzcrash.c")
list(REMOVE_ITEM IRR_SRC_FILES_JPEGLIB
	"${IRR_SRC_DIR}/jpeglib/cjpeg.c"
	"${IRR_SRC_DIR}/jpeglib/djpeg.c"
	"${IRR_SRC_DIR}/jpeglib/rdjpgcom.c"
	"${IRR_SRC_DIR}/jpeglib/wrjpgcom.c"
	"${IRR_SRC_DIR}/jpeglib/example.c"
	"${IRR_SRC_DIR}/jpeglib/jmemdos.c"
	"${IRR_SRC_DIR}/jpeglib/jmemmac.c"
	"${IRR_SRC_DIR}/jpeglib/jmemname.c"
	"${IRR_SRC_DIR}/jpeglib/jmemansi.c"
	"${IRR_SRC_DIR}/jpeglib/jpegtran.c")
list(REMOVE_ITEM IRR_SRC_FILES_LIBPNG
	"${IRR_SRC_DIR}/libpng/pngtest.c")
list(REMOVE_ITEM IRR_SRC_FILES_ZLIB
	"${IRR_SRC_DIR}/zlib/gzclose.c"
	"${IRR_SRC_DIR}/zlib/gzlib.c"
	"${IRR_SRC_DIR}/zlib/gzread.c"
	"${IRR_SRC_DIR}/zlib/gzwrite.c"
	"${IRR_SRC_DIR}/zlib/infback.c")
	
# Group files
# -------------------------------------------------------------------------------------------------
source_group(Irrlicht\\engine           FILES ${IRR_SRC_FILES})
source_group(Irrlicht\\libs\\aesGladman FILES ${IRR_SRC_FILES_AESGLADMAN})
source_group(Irrlicht\\libs\\bzip2      FILES ${IRR_SRC_FILES_BZIP2})
source_group(Irrlicht\\libs\\jpeglib    FILES ${IRR_SRC_FILES_JPEGLIB})
source_group(Irrlicht\\libs\\libpng     FILES ${IRR_SRC_FILES_LIBPNG})
source_group(Irrlicht\\libs\\lzma       FILES ${IRR_SRC_FILES_LZMA})
source_group(Irrlicht\\libs\\zlib       FILES ${IRR_SRC_FILES_ZLIB})

# Irrlicht target
# -------------------------------------------------------------------------------------------------
add_library(Irrlicht
	${IRR_SRC_FILES}
	${IRR_SRC_FILES_AESGLADMAN}
	${IRR_SRC_FILES_BZIP2}
	${IRR_SRC_FILES_JPEGLIB}
	${IRR_SRC_FILES_LIBPNG}
	${IRR_SRC_FILES_LZMA}
	${IRR_SRC_FILES_ZLIB})

# Target properties (for compilation & export)
# -------------------------------------------------------------------------------------------------
target_include_directories(Irrlicht
	PUBLIC ${IRR_ROOT_DIR}/include
	PRIVATE ${IRR_SRC_DIR}
	PRIVATE ${IRR_SRC_DIR}/aesGladman
	PRIVATE ${IRR_SRC_DIR}/bzip2
	PRIVATE ${IRR_SRC_DIR}/jpeglib
	PRIVATE ${IRR_SRC_DIR}/libpng
	PRIVATE ${IRR_SRC_DIR}/lzma
	PRIVATE ${IRR_SRC_DIR}/zlib)
if(NOT ${BUILD_SHARED_LIBS})
	target_compile_definitions(Irrlicht PUBLIC _IRR_STATIC_LIB_)
endif()

# Per platform config
# -------------------------------------------------------------------------------------------------
set(IRR_PLATFORM "Linux")
if(WIN32)
	# Export symbols if in dll
	target_compile_definitions(Irrlicht PRIVATE IRRLICHT_EXPORTS)

	# Multi processor compilation
	target_compile_options(Irrlicht PRIVATE "/MP")

	# Fast math options
	if(${IRR_FAST_MATH})
		target_compile_options(Irrlicht
			PRIVATE "/fp:fast"
			PRIVATE "/arch:SSE2")
	endif()
	
	# Platform (note: mingw not supported here)
	if(${CMAKE_SIZEOF_VOID_P} EQUAL 8)
		set(IRR_PLATFORM "Win64-VisualStudio")
	else()
		set(IRR_PLATFORM "Win32-VisualStudio")
	endif()
	
	# TEMP: no DirectX 9
	target_compile_definitions(Irrlicht PRIVATE NO_IRR_COMPILE_WITH_DIRECT3D_9_)
endif()

# Last step, output directories
# -------------------------------------------------------------------------------------------------
setup_target_paths(Irrlicht "${IRR_ROOT_DIR}/lib/${IRR_PLATFORM}" "${IRR_ROOT_DIR}/bin/${IRR_PLATFORM}")
