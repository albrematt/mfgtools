cmake_minimum_required(VERSION 3.12)
project(mfgtools VERSION 1.4.43 LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_SKIP_RPATH ON)

option(BUILD_SHARED_LIBS "Build Shared Libraries" OFF)
option(BUILD_DOC "Build documentation" OFF)
option(BUILD_UUU "UUU executable" ON)
option(SKIP_UUU_INSTALL "Skip installation" OFF)
option(ENABLE_OPENSSL "Enable use of OpenSSL" ON)

execute_process(
		COMMAND git describe --tags --long
		WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
		OUTPUT_VARIABLE MFGTOOLS_GIT_VERSION
		OUTPUT_STRIP_TRAILING_WHITESPACE
		ERROR_QUIET
		)

find_package(ZLIB)
if(NOT ZLIB_FOUND)
	set(BUILD_TESTING OFF CACHE INTERNAL "")
	set(SKIP_INSTALL_LIBRARIES ON CACHE INTERNAL "")
	add_subdirectory(zlib)
	set(ZLIB_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/zlib" CACHE STRING "" FORCE)
	set(ZLIB_LIBRARIES zlib CACHE STRING "" FORCE)
	set(ZLIB_FOUND TRUE CACHE STRING "" FORCE)
endif()

find_package(BZip2)
if(NOT BZip2_FOUND)
	set(BZIP2_SKIP_TOOLS ON CACHE INTERNAL "")
	add_subdirectory(bzip2)
	set(BZIP2_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/bzip2" CACHE STRING "" FORCE)
	set(BZIP2_LIBRARIES bz2 CACHE STRING "" FORCE)
	set(BZIP2_FOUND TRUE CACHE STRING "" FORCE)
endif()

#find_package(libzip)
#if(NOT libzip_FOUND)
#	add_subdirectory(libzip)
#	set(LIBZIP_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/libzip" CACHE STRING "" FORCE)
#	set(LIBZIP_LIBRARIES libzip CACHE STRING "" FORCE)
#	set(LIBZIP_FOUND TRUE CACHE STRING "" FORCE)
#endif()

find_package(libusb)
if(NOT libusb_FOUND)
	add_subdirectory(libusb)
	set(LIBUSB_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/libusb/libusb" CACHE STRING "" FORCE)
	set(LIBUSB_LIBRARIES libusb CACHE STRING "" FORCE)
	set(LIBUSB_FOUND TRUE CACHE STRING "" FORCE)
endif()

add_subdirectory(libuuu)
if(BUILD_UUU)
	add_subdirectory(uuu)
endif()

if(BUILD_DOC)
	# Check if Doxygen is installed
	find_package(Doxygen)
	if(DOXYGEN_FOUND)
	    # Set input and output files
	    set(DOXYGEN_IN ${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in)
	    set(DOXYGEN_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

	    # Request to configure the file
	    configure_file(${DOXYGEN_IN} ${DOXYGEN_OUT} @ONLY)
	    message("Doxygen build started")

	    # Note the option ALL which allows to build the docs together with the application
	    add_custom_target( doc_doxygen ALL
		COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_OUT}
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
		COMMENT "Generating documentation with Doxygen"
		VERBATIM )
	else()
		message("Doxygen need to be installed to generate the doxygen documentation")
	endif()
endif()
