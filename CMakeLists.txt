cmake_minimum_required(VERSION 3.8.2 FATAL_ERROR)
project(basisu VERSION 1.0.5 LANGUAGES C CXX)

# Add property to allow making folders for basis in IDEs.
set_property(GLOBAL PROPERTY USE_FOLDERS ON)  

option(BUILD_SHARED_LIBS "Build Shared Libraries" OFF)
option(BUILD_TESTS "Build test applications" ON)
option(BASISU_BUILD_TOOL "Build the tool" ON)
option(BASISU_INSTALL "Installs basis universal" ON)
option(BASISU_INSTALL_TOOL "Installs the tool" ON)
option(BASISU_OPENMP "Allows OpenMP support" ON)

# GNU filesystem conventions
include(GNUInstallDirs)

# Windows build shared libraries
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

if(BASISU_OPENMP AND NOT TARGET OpenMP::OpenMP_CXX)
	find_package(OpenMP REQUIRED)

	# For CMake < 3.9, we need to make the OpenMP target ourselves
	if (NOT TARGET OpenMP::OpenMP_CXX)
		find_package(Threads REQUIRED)
		add_library(OpenMP::OpenMP_CXX IMPORTED INTERFACE)
		set_property(TARGET OpenMP::OpenMP_CXX
					PROPERTY INTERFACE_COMPILE_OPTIONS ${OpenMP_CXX_FLAGS})
		# Only works if the same flag is passed to the linker; use CMake 3.9+ otherwise (Intel, AppleClang)
		set_property(TARGET OpenMP::OpenMP_CXX
					PROPERTY INTERFACE_LINK_LIBRARIES ${OpenMP_CXX_FLAGS} Threads::Threads)
	endif()
endif()

add_subdirectory(src)

if(BUILD_TESTS)
	include(CTest)
endif()
