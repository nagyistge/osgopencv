# Target name
SET(LIB_TARGETNAME OsgOpenCVLib)
SET(TARGET_TARGETNAME OsgOpenCVExample)

# Target source files
SET(LIB_SRC
	opencvimagestream.cpp
)

# Target header files
SET(LIB_H
	opencvimagestream.h
)

# Target source files
SET(TARGET_SRC
	main.cpp
)

# Target headerfiles
SET(TARGET_H
)

#####################################################################
# Create executable
#####################################################################

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4351 /wd4510 /wd4610")
ADD_LIBRARY(${LIB_TARGETNAME} STATIC ${LIB_SRC} ${LIB_H})

ADD_EXECUTABLE(${TARGET_TARGETNAME} ${TARGET_SRC} ${TARGET_H})

#####################################################################
# Linker options
#####################################################################

# Link to open gl libs
TARGET_LINK_LIBRARIES(${TARGET_TARGETNAME}  ${OPENGL_LIBRARIES} )

# Link to OpenSceneGraph libs
TARGET_LINK_LIBRARIES(${TARGET_TARGETNAME}  ${OPENSCENEGRAPH_LIBRARIES} )

# Link to OpenCV libs
TARGET_LINK_LIBRARIES(${TARGET_TARGETNAME}  ${OPENCV_LIBRARIES} )

# Link to example to OsgOpenCV lib
TARGET_LINK_LIBRARIES(${TARGET_TARGETNAME}  ${LIB_TARGETNAME} )

####################################################################
# Create user file for correct environment string
#####################################################################
IF(WIN32)
    IF(MSVC)
		IF (OPENSCENEGRAPH_FOUND)
			# Build path
			SET(USERFILE_ENVIRONMENT_DEBUG "PATH=")
			SET(USERFILE_ENVIRONMENT_RELEASE "PATH=")

			# Add OpenSceneGraph to path
			IF (EXISTS "${OSG_DIR}/bin")
				SET(OSG_BIN_DIR "${OSG_DIR}/bin" CACHE PATH "Path where to find OpenSceneGraph binaries")
			ELSE (EXISTS "${OSG_DIR}/bin")
				SET (OSG_BIN_DIR CACHE PATH "Path where to find OpenSceneGraph binaries")
			ENDIF(EXISTS "${OSG_DIR}/bin")
			SET(USERFILE_ENVIRONMENT_DEBUG "${USERFILE_ENVIRONMENT_DEBUG}${OSG_BIN_DIR}")
			SET(USERFILE_ENVIRONMENT_RELEASE "${USERFILE_ENVIRONMENT_RELEASE}${OSG_BIN_DIR}")
			
			IF (OSG_THIRD_PARTY_DIR)
				# Add third party to path
				SET(USERFILE_ENVIRONMENT_DEBUG "${USERFILE_ENVIRONMENT_DEBUG};${OSG_THIRD_PARTY_DIR}/bin")
				SET(USERFILE_ENVIRONMENT_RELEASE "${USERFILE_ENVIRONMENT_RELEASE};${OSG_THIRD_PARTY_DIR}/bin")
			ELSE (OSG_THIRD_PARTY_DIR)
				MESSAGE("Warning: OpenSceneGraph 3rd Party Directory not found.")
			ENDIF(OSG_THIRD_PARTY_DIR)
			
			IF (OPENCV_BIN_DIR)
				# Add third party to path
				SET(USERFILE_ENVIRONMENT_DEBUG "${USERFILE_ENVIRONMENT_DEBUG};${OPENCV_BIN_DIR}")
				SET(USERFILE_ENVIRONMENT_RELEASE "${USERFILE_ENVIRONMENT_RELEASE};${OPENCV_BIN_DIR}")
			ELSE (OPENCV_BIN_DIR)
				MESSAGE("Warning: OpenCV binary Directory not found.")
			ENDIF(OPENCV_BIN_DIR)

			# For visual studio 2010 (MSVC10), 2012 (MSVC11) and 2013 (MSVC12)
			IF(MSVC10 OR MSVC11 OR MSVC12)
				CONFIGURE_FILE(osgopencv.vcxproj.template ${TARGET_TARGETNAME}.vcxproj.user @ONLY)
			ENDIF()

		ENDIF (OPENSCENEGRAPH_FOUND)
    ENDIF(MSVC)
ENDIF(WIN32)
