CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

PROJECT(uriparser C)

OPTION (DISABLE_CHAR "Disable building ANSI functions")
OPTION (DISABLE_WCHAR_T "Disable building UNICODE functions")

IF (DISABLE_CHAR AND DISABLE_WCHAR_T)
	MESSAGE (FATAL_ERROR "Setting DISABLE_CHAR and DISABLE_WCHAR_T is invalid.")
ENDIF (DISABLE_CHAR AND DISABLE_WCHAR_T)

SET (uriparser_Source_Files
	src/UriCommon.c
	src/UriCompare.c
	src/UriEscape.c
	src/UriFile.c
	src/UriIp4Base.c
	src/UriIp4.c
	src/UriNormalizeBase.c
	src/UriNormalize.c
	src/UriParseBase.c
	src/UriParse.c
	src/UriQuery.c
	src/UriRecompose.c
	src/UriResolve.c
	src/UriShorten.c
)

IF (DISABLE_CHAR)
	ADD_DEFINITIONS (-DURI_NO_ANSI)
ENDIF (DISABLE_CHAR)

IF (DISABLE_WCHAR_T)
	ADD_DEFINITIONS (-DURI_NO_UNICODE)
ENDIF (DISABLE_WCHAR_T)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

ADD_LIBRARY (uriparser SHARED ${uriparser_Source_Files})
target_include_directories(uriparser PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

INSTALL (TARGETS uriparser
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib)

INSTALL (DIRECTORY include/ DESTINATION include)
