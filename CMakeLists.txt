cmake_minimum_required(VERSION 3.13)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "default build type")
endif()

project(PDCurses
LANGUAGES C
VERSION 3.9
DESCRIPTION "PDCurses - a curses library for environments that don't fit the termcap/terminfo model."
HOMEPAGE_URL "https://github.com/wmcbrine/PDCurses")

function(target_public_headers target)
  # append headers each time, overwriting previous
  foreach(f ${ARGN})
    get_filename_component(_abs ${f} ABSOLUTE)
    list(APPEND _hdr ${_abs})
  endforeach()
  list(APPEND ${target}_headers ${_hdr})

  set(${target}_headers ${${target}_headers} CACHE INTERNAL "${target} public headers")

  set_target_properties(${target} PROPERTIES PUBLIC_HEADER "${${target}_headers}")  # quotes necessary
endfunction()

add_library(pdcurses)
target_public_headers(pdcurses curses.h curspriv.h)
target_include_directories(pdcurses
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>)

add_library(CURSES::CURSES ALIAS pdcurses)


add_subdirectory(common)
add_subdirectory(pdcurses)

if(WIN32)
  add_subdirectory(wincon)
elseif(CMAKE_SYSTEM_NAME STREQUAL Linux)
  find_package(X11 REQUIRED)

  find_package(PkgConfig REQUIRED)

  # xt.pc is broken on Ubuntu
  pkg_check_modules(_xt xt)

  find_path(_xt_inc REQUIRED
  NAMES Intrinsic.h
  HINTS ${_xt_INCLUDE_DIRS}
  PATHS /usr/include
  PATH_SUFFIXES X11)

  pkg_check_modules(_xaw REQUIRED xaw7)

  list(APPEND X11_INCLUDE_DIR ${_xt_inc})
  list(APPEND X11_LIBRARIES ${_xaw_LIBRARIES})

  target_include_directories(pdcurses PRIVATE ${X11_INCLUDE_DIR})
  target_link_libraries(pdcurses PRIVATE ${X11_LIBRARIES} X11::Xt X11::Xpm X11::Xmu)
  add_subdirectory(x11)
endif()

if(NOT CMAKE_SOURCE_DIR STREQUAL PROJECT_SOURCE_DIR)
  # being used from other package
  return()
endif()

add_subdirectory(demos)

INSTALL(TARGETS pdcurses
        PUBLIC_HEADER DESTINATION include
)
