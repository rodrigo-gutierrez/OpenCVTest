cmake_minimum_required(VERSION 3.10)

project( OpenCVTest )

find_package( OpenCV REQUIRED )

include_directories( ${OpenCV_INCLUDE_DIRS} )

add_executable( removeRedEyes removeRedEyes.cpp )

target_link_libraries( removeRedEyes ${OpenCV_LIBS} )

set( source "${OpenCV_INSTALL_PATH}/bin/Release" )
set( destination "${CMAKE_CURRENT_BINARY_DIR}/Release" )

if( WIN32 )

	macro( cmake_to_win_path CMakePath WinPath )
		string( REGEX REPLACE "/" "\\\\" ${WinPath} "${CMakePath}" )
	endmacro()

	cmake_to_win_path( ${source} winSource )
	cmake_to_win_path( ${destination} winDestination )

	message( "Converted \"${source}\" to \"${winSource}\"." )
	message( "Converted \"${destination}\" to \"${winDestination}\"." )

	add_custom_command(
		TARGET removeRedEyes POST_BUILD
		COMMAND xcopy ${winSource}\\*.dll ${winDestination} /d /i /y
		DEPENDS ${destination}
		COMMENT "Copy all OpenCV DLLs from ${winSource} to ${winDestination}"
	)

else()

	add_custom_command(
		TARGET removeRedEyes POST_BUILD
		COMMAND cp ${source}/*.dll ${destination}
		DEPENDS ${destination}
		COMMENT "Copy all OpenCV DLLs from ${source} to ${destination}"
	)

endif()
