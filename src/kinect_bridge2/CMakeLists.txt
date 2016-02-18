if( WIN32 )
	cmake_minimum_required(VERSION 2.4.6)
	project(kinect_bridge2)

	set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

	#set the default path for built libraries to the "lib" directory
	set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

	include_directories( include )
	link_directories( lib )
	set( DEPS_DIR "${PROJECT_SOURCE_DIR}/deps" )

	if( "${CMAKE_GENERATOR}" MATCHES ".*Win64*" )
		set( BUILD_TYPE x64 )
	else()
		set( BUILD_TYPE Win32 )
	endif()

# libpng
	set( PNG_DEPDIR "${DEPS_DIR}/libpng1617" )
	set( PNG_INCDIR "${PNG_DEPDIR}" )

	if( ${BUILD_TYPE} STREQUAL x64 )
		set( PNG_DLLDIR "${PNG_DEPDIR}/projects/vstudio/x64/Release" )
		set( PNG_LIBDIR "${PNG_DEPDIR}/projects/vstudio/x64" )
	else()
		set( PNG_DLLDIR "${PNG_DEPDIR}/projects/vstudio/Release" )
		set( PNG_LIBDIR "${PNG_DEPDIR}/projects/vstudio" )
	endif()

	set( PNG_LIBS libpng16 zlib )

# libsndfile
	set( SNDFILE_DEPDIR "${DEPS_DIR}/libsndfile" )
	set( SNDFILE_INCDIR "${SNDFILE_DEPDIR}/include" )

	if( ${BUILD_TYPE} STREQUAL x64 )
		set( SNDFILE_DLLDIR "${SNDFILE_DEPDIR}/bin64" )
		set( SNDFILE_LIBDIR "${SNDFILE_DEPDIR}/lib64" )
	else()
		set( SNDFILE_DLLDIR "${SNDFILE_DEPDIR}/bin" )
		set( SNDFILE_LIBDIR "${SNDFILE_DEPDIR}/lib" )
	endif()

	set( SNDFILE_LIBS libsndfile-1 )

# libpoco
	set( POCO_LIBNAMES Foundation Net Util JSON XML )

	set( POCO_DEPDIR "${DEPS_DIR}/poco-1.6.0" )
	foreach( poco_lib ${POCO_LIBNAMES} )
		set( POCO_INCDIR ${POCO_INCDIR} ${POCO_DEPDIR}/${poco_lib}/include )
#	include_directories( "${POCO_DEPDIR}/${poco_lib}/include" )
		set( POCO_LIBS ${POCO_LIBS} Poco${poco_lib} )
	endforeach()

	if( ${BUILD_TYPE} STREQUAL x64 )
		set( POCO_DLLDIR "${POCO_DEPDIR}/bin64" )
		set( POCO_LIBDIR "${POCO_DEPDIR}/lib64" )
	else()
		set( POCO_DLLDIR "${POCO_DEPDIR}/bin" )
		set( POCO_LIBDIR "${POCO_DEPDIR}/lib" )
	endif()

# speech
	set( SPEECH_INCDIR "$<1:$(ProgramW6432)>/Microsoft SDKs/Speech/v11.0/Include" )
	set( SPEECH_LIBDIR "$(ProgramW6432)/Microsoft SDKs/Speech/v11.0/Lib" )

# kinect
	set( KINECT_INCDIR "$<1:$(KINECTSDK20_DIR)>/inc" )

	if( ${BUILD_TYPE} STREQUAL x64 )
		set( KINECT_LIBDIR "$(KINECTSDK20_DIR)/lib/x64" )
	else()
		set( KINECT_LIBDIR "$(KINECTSDK20_DIR)/lib/x86" )
	endif()

	message( "${KINECT_INCDIR}" )
	message( "${KINECT_LIBDIR}" )

	set( KINECT_LIBS kinect20 )

	message( "${POCO_DLLDIR}" )

	add_subdirectory( src )
	add_subdirectory( exe )


else()
	cmake_minimum_required(VERSION 2.8.3)
	project(kinect_bridge2)

	add_definitions( "-std=c++0x" ) #c++ 11 compiling

	find_package(catkin REQUIRED COMPONENTS
	  geometry_msgs
	  message_generation
	  message_runtime
	  roscpp
	  std_msgs
	  tf
	)

	set( Poco_DIR cmake)

	set( PNG_INCDIR "/usr/local/include" )
	set( PNG_LIBDIR "/usr/local/lib" )
	set( PNG_LIBS png12 z )

	find_package(Poco REQUIRED COMPONENTS Foundation Net Util)

	add_message_files(
	   FILES
	   KinectBodies.msg
	   KinectBody.msg
	   KinectJoint.msg
	   KinectSpeech.msg
	   KinectSpeechPhrase.msg
	)

	generate_messages(
	   DEPENDENCIES
	   std_msgs  # Or other packages containing msgs
	   geometry_msgs
	)

	catkin_package(
	  INCLUDE_DIRS include
	  # LIBRARIES kinect_bridge2
	   CATKIN_DEPENDS roscpp tf std_msgs geometry_msgs
	   DEPENDS Poco
	)

	include_directories(include ${roscpp_INCLUDE_DIRS} 
	  ${catkin_INCLUDE_DIRS} ${Poco_INCLUDE_DIRS}
	)

	include_directories( "${PNG_INCDIR}" )
	link_directories( "${PNG_LIBDIR}" )

	add_library(atomics
	   src/atomics/assert.cpp
	   src/atomics/binary_stream.cpp
	   src/atomics/exceptions.cpp
	   src/atomics/handle.cpp
	   src/atomics/mem_block.cpp
	   src/atomics/memory_pool_allocator.cpp
	   src/atomics/print.cpp
	   src/atomics/wrapper.cpp
	)

	add_library(messages
	   src/messages/audio_message.cpp
	   src/messages/binary_codec.cpp
	   src/messages/binary_message.cpp
	   src/messages/codec.cpp
	   src/messages/container_messages.cpp
	   src/messages/exceptions.cpp
	   src/messages/geometry_messages.cpp
	   src/messages/gzip_codec.cpp
	   src/messages/image_message.cpp
	   src/messages/input_file_device.cpp
	   src/messages/input_tcp_device.cpp
	   src/messages/io_file_device.cpp
	   src/messages/kinect_messages.cpp
	   src/messages/message.cpp
	   src/messages/message_coder.cpp
	   src/messages/output_file_device.cpp
	   src/messages/output_tcp_device.cpp
	   src/messages/png_image_message.cpp
	   src/messages/serializable_message.cpp
	   src/messages/utility_messages.cpp
	   src/messages/wav_audio_message.cpp
	)

	add_executable(kinect_client src/kinect_client.cpp)

	target_link_libraries(kinect_client
	   ${catkin_LIBRARIES}
	   ${roscpp_LIBRARIES}
	   atomics
	   messages
	   ${Poco_LIBRARIES}
	   ${PNG_LIBS} 
	)


endif()
