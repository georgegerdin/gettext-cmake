cmake_minimum_required(VERSION 2.8)

project(gettext)

# Required modules
include(GNUInstallDirs)
include(CheckIncludeFiles)

option(BUILD_SHARED "build dynamic library" ON)
option(BUILD_STATIC "build static library" ON)

set(SRCS intl/bindtextdom.c intl/dcgettext.c intl/dcigettext.c 
	intl/dcngettext.c intl/dgettext.c intl/dngettext.c 
	intl/explodename.c intl/finddomain.c intl/gettext.c 
	intl/hash-string.c intl/intl-compat.c intl/l10nflist.c 
	intl/langprefs.c intl/loadmsgcat.c intl/localcharset.c 	
	intl/localealias.c intl/localename.c intl/lock.c intl/log.c 
	intl/ngettext.c intl/osdep.c intl/plural-exp.c intl/plural.c 
	intl/printf.c intl/relocatable.c intl/textdomain.c 
	intl/version.c config.h intl/libgnuintl.h )
	
foreach(e ${SRCS})
	set(INTL_SRCS "${INTL_SRCS}" "${CMAKE_CURRENT_LIST_DIR}/source/gettext-runtime/${e}")
endforeach()

set(CMAKE_DEBUG_POSTFIX "d")

include_directories("${CMAKE_CURRENT_LIST_DIR}/source/gettext-runtime")

add_definitions("-DIN_LIBINTL"
			    "-DENABLE_RELOCATABLE=1"
				"-DIN_LIBRARY"
				"-DNO_XMALLOC"
				"-Dset_relocation_prefix=libintl_set_relocation_prefix"
				"-Drelocate=libintl_relocate"
				"-DDEPENDS_ON_LIBICONV=1"
				"-DHAVE_CONFIG_H"
				"-D_CRT_SECURE_NO_WARNINGS"
				"-DLOCALEDIR=\"\""
				"-DLOCALE_ALIAS_PATH=\"\""
				"-DLIBDIR=\"\""
				)

if(BUILD_SHARED)
	add_library(intl SHARED ${INTL_SRCS})
	list(APPEND DEFINITIONS "BUILDING_LIBINTL")
	list(APPEND DEFINITIONS "BUILDING_DLL")
	set_target_properties(intl PROPERTIES COMPILE_DEFINITIONS "${DEFINITIONS}")
	install(TARGETS intl RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif()
if(BUILD_STATIC)
	add_library(intl-static STATIC ${INTL_SRCS})
	install(TARGETS intl-static RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif()

install(FILES "${CMAKE_CURRENT_LIST_DIR}/source/gettext-runtime/intl/libgnuintl.h" DESTINATION ${CMAKE_INSTALL_FULL_INCLUDEDIR})
