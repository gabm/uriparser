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

FILE (READ src/uriparser.exports URIPARSER_EXPORTS)

IF (UNIX)
	IF (NOT DISABLE_CHAR)
		STRING (REGEX REPLACE "([^\n]+)\n" "\t\t\\1A;\n"
			URIPARSERA_EXPORTS ${URIPARSER_EXPORTS})
	ENDIF (NOT DISABLE_CHAR)

	IF (NOT DISABLE_WCHAR_T)
		STRING (REGEX REPLACE "([^\n]+)\n" " \t\t\\1W;\n"
			URIPARSERW_EXPORTS ${URIPARSER_EXPORTS})
	ENDIF (NOT DISABLE_WCHAR_T)

	CONFIGURE_FILE (src/uriparser.sym.in src/uriparser.sym)
	SET (uriparser_DEF src/uriparser.def)
ELSEIF (WIN32)
	IF (NOT DISABLE_CHAR)
		STRING (REGEX REPLACE "([^\n]+)\n" "\t\\1A\n"
			URIPARSERA_EXPORTS ${URIPARSER_EXPORTS})
	ENDIF (NOT DISABLE_CHAR)

	IF (NOT DISABLE_WCHAR_T)
		STRING (REGEX REPLACE "([^\n]+)\n" "\t\\1W\n"
			URIPARSERW_EXPORTS ${URIPARSER_EXPORTS})
	ENDIF (NOT DISABLE_WCHAR_T)

	CONFIGURE_FILE (src/uriparser.def.in src/uriparser.def)
	SET (uriparser_DEF src/uriparser.def)
	INCLUDE_DIRECTORIES (include)
ENDIF (UNIX)

ADD_LIBRARY (uriparser SHARED ${uriparser_Source_Files} ${uriparser_DEF})

IF (UNIX)
	SET_TARGET_PROPERTIES (uriparser PROPERTIES LINK_FLAGS "-Wl,--version-script=${CMAKE_BINARY_DIR}/src/uriparser.sym")
ENDIF (UNIX)

INSTALL (TARGETS uriparser
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib)

INSTALL (DIRECTORY include/ DESTINATION include)
