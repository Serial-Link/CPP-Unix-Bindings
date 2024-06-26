cmake_minimum_required(VERSION 3.10)

set(VERSION_MAJOR 0)
set(VERSION_MINOR 2)
set(VERSION_PATCH 0)

set(PROJECT_N CPP-Unix-Bindings)
project(${PROJECT_N} VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH})

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

file(GLOB_RECURSE SRCS ${PROJECT_SOURCE_DIR}/src/**.cpp)

set(LIB true)

configure_file(versioning/version_config.cpp.in ${PROJECT_SOURCE_DIR}/src/version_config.cpp)

# a macro that gets all of the header containing directories. 
MACRO(header_directories return_list includes_base_folder extention )
    FILE(GLOB_RECURSE new_list ${includes_base_folder}/*.${extention})
    SET(dir_list "")
    FOREACH(file_path ${new_list})
        GET_FILENAME_COMPONENT(dir_path ${file_path} PATH)
        SET(dir_list ${dir_list} ${dir_path})
    ENDFOREACH()
    LIST(REMOVE_DUPLICATES dir_list)
    SET(${return_list} ${dir_list})
ENDMACRO()

# using said macro.
header_directories(INCLUDES ${PROJECT_SOURCE_DIR}/src/ hpp)

message("src files:")
foreach(file ${SRCS})
        message(STATUS ${file})
endforeach()

message("include directories:")
foreach(dir ${INCLUDES})
        message(STATUS ${dir})
endforeach()

if(LIB)
    add_library(${PROJECT_N} SHARED ${SRCS})
else()
    add_executable(${PROJECT_N} ${SRCS}) 
endif(LIB)

set_target_properties(${PROJECT_N} PROPERTIES 
                              VERSION ${PROJECT_VERSION}
                            SOVERSION ${PROJECT_VERSION_MAJOR})

target_include_directories(${PROJECT_N} PUBLIC include)
