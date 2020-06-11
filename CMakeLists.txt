PROJECT(bcu)
cmake_minimum_required(VERSION 3.4)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
#set(CMAKE_BUILD_TYPE Debug)
find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBUSB REQUIRED libusb-1.0)
project(bcu)

include(conan_paths.cmake)

add_executable(bcu bcu.c port.c chip.c board.c bcu_parser.c bcu_yaml.c)

include_directories(${PROJECT_SOURCE_DIR})

find_package(libftdi)
find_package(libyaml)
find_package(libusb)
find_package(zlib)

if(LIBFTDI_FOUND)
    target_link_libraries (bcu ${LIBFTDI_LIBRARIES})
    include_directories(${LIBFTDI_INCLUDE_DIRS}include)
endif()

if(LIBYAML_FOUND)
    target_link_libraries (bcu libyaml::libyaml)
endif()

if(LIBUSB_FOUND)
    target_link_libraries (bcu ${LIBUSB_LIBRARIES} -lm)
    include_directories(${LIBUSB_INCLUDE_DIRS})
endif()

if(LIBZLIB_FOUND)
    target_link_libraries (bcu ${ZLIB_LIBRARIES})
endif()

#below is for version number generation
#execute_process(
#  COMMAND git describe --tags --long
#  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
#  OUTPUT_VARIABLE GIT_COMMIT_VERSION
#  OUTPUT_STRIP_TRAILING_WHITESPACE
#)
#configure_file(
#  ${CMAKE_SOURCE_DIR}/version.h.in
#  ${CMAKE_BINARY_DIR}/version.h
#)

execute_process( COMMAND ./create_version_h.sh )
