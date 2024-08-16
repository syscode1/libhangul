cmake_minimum_required(VERSION 3.20)

project(libhangul C)

# 공유 라이브러리(DLL) 생성
add_library(hangul SHARED
    hangul/hangulctype.c
    hangul/hangulinputcontext.c
    hangul/hangulkeyboard.c
    hangul/hanja.c
)

target_include_directories(hangul PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/win32
)

if(MSVC)
    target_compile_options(hangul PRIVATE /utf-8)
    
    # DLL 내보내기 매크로 정의
    target_compile_definitions(hangul PRIVATE
        HANGUL_EXPORT
        HANGUL_EXPORT_SYMBOLS
    )

    # Debug와 Release 구성에 따른 런타임 라이브러리 설정
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    
    # 추가적인 컴파일 옵션 설정
    target_compile_options(hangul PRIVATE
        $<$<CONFIG:Debug>:/MTd>
        $<$<CONFIG:Release>:/MT>
    )

    set_target_properties(hangul PROPERTIES LINK_FLAGS "/DEF:${CMAKE_CURRENT_SOURCE_DIR}/libhangul.def")
endif()

target_compile_definitions(hangul PRIVATE
    WIN32
    ENABLE_EXTERNAL_KEYBOARDS=0
    _CRT_NONSTDC_NO_DEPRECATE
    _CRT_SECURE_NO_WARNINGS
    $<$<CONFIG:Debug>:_DEBUG>
)

# Windows에서 DLL 출력 이름 설정
if(WIN32)
    set_target_properties(hangul PROPERTIES
        OUTPUT_NAME "libhangul"
        PREFIX ""
        DEBUG_POSTFIX "d"
    )
endif()

# 버전 정보 설정
set_target_properties(hangul PROPERTIES
    VERSION 0.1.0
    SOVERSION 1
)

# 출력 디렉토리 설정
set_target_properties(hangul PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
)
