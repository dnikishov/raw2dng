# add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

# The version number.
SET(RAW2DNG_VERSION_MAJOR 1)
SET(RAW2DNG_VERSION_MINOR 2)
SET(RAW2DNG_VERSION_PATCH 2)

# configure a header file to pass some of the CMake settings
# to the source code
CONFIGURE_FILE ( "${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake"
                 "${CMAKE_CURRENT_BINARY_DIR}/config.h" )

SET( EXIV2_MIN_VERSION "0.21" )
FIND_PACKAGE( Exiv2 )
INCLUDE_DIRECTORIES( ${EXIV2_INCLUDE_DIR} )
ADD_DEFINITIONS( ${EXIV2_DEFINITIONS}) 

SET( LIBRAW_MIN_VERSION "0.17" )
FIND_PACKAGE( LibRaw )
INCLUDE_DIRECTORIES( ${LIBRAW_INCLUDE_DIR} )
ADD_DEFINITIONS(${LIBRAW_DEFINITIONS})

FIND_PACKAGE( ZLIB )
INCLUDE_DIRECTORIES( ${ZLIB_INCLUDE_DIR} )
ADD_DEFINITIONS( ${ZLIB_DEFINITIONS} )

INCLUDE_DIRECTORIES( ${raw2dng_SOURCE_DIR}/libdng 
                     ${raw2dng_SOURCE_DIR}/libdng/dng-sdk/source 
                     ${PROJECT_BINARY_DIR} )

ADD_EXECUTABLE( raw2dng
                ${CMAKE_CURRENT_SOURCE_DIR}/raw2dng.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/rawConverter.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/processor.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/raw.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/rawexiv.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/vendor_raw.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/dng_input.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/sony/ILCE7.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/fuji/common.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/xiaomi/yi.cpp
                ${CMAKE_CURRENT_SOURCE_DIR}/negativeProcessor/variousVendorProcessor.cpp )

TARGET_LINK_LIBRARIES( raw2dng dng ${ZLIB_LIBRARIES} ${LIBRAW_LIBRARIES} ${EXIV2_LIBRARIES} )
TARGET_COMPILE_OPTIONS( raw2dng PRIVATE -fexceptions -std=c++11 )

INSTALL(TARGETS raw2dng DESTINATION bin)
