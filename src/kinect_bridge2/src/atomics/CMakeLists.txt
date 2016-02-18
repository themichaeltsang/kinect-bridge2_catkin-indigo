if( WIN32 )
	include_directories( "${POCO_INCDIR}" )
	link_directories( "${POCO_LIBDIR}" )

	add_definitions( "-std=c++11" )
	FILE( GLOB src_files RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} *.cpp )

	add_library( atomics STATIC ${src_files} )
endif()
