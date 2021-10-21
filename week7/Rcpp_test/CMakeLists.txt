cmake_minimum_required(VERSION 3.5)

project(Rcpp_test LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


find_package(LibR)
if(${LIBR_FOUND})
else()
  message(FATAL_ERROR "No R...")
endif()

message(STATUS ${CMAKE_SOURCE_DIR})
execute_process(
    COMMAND ${LIBR_EXECUTABLE} "--slave" "-e" "stopifnot(require('Rcpp'));cat(Rcpp:::Rcpp.system.file('include'))"
    OUTPUT_VARIABLE LIBRCPP_INCLUDE_DIRS
    )
include_directories(BEFORE ${LIBR_INCLUDE_DIRS})
message(STATUS ${LIBR_INCLUDE_DIRS})
include_directories(BEFORE ${LIBRCPP_INCLUDE_DIRS})

#message(STATUS ${LIBRCPP_INCLUDE_DIRS})
#add_custom_target(RcppPackage ALL
#	COMMAND find ${CMAKE_SOURCE_DIR} -name "*.o" -exec rm "{}" "\;"
#	COMMAND find ${CMAKE_SOURCE_DIR} -name "*.so" -exec rm "{}" "\;"
#	COMMAND ${LIBR_EXECUTABLE} "--slave" "-e" "\"stopifnot(require(roxygen2));roxygenize('${CMAKE_SOURCE_DIR}',roclets=c('rd','collate','namespace'))\""
#	COMMAND ${LIBR_EXECUTABLE} CMD INSTALL "${CMAKE_SOURCE_DIR}")


link_directories("/usr/lib/R/lib")
add_library(libR SHARED IMPORTED)
message(STATUS "project link_libs:" ${PROJECT_LINK_LIBS})
if( NOT PROJECT_LINK_LIBS OR $PROJECT_LINK_LIBS STREQUAL "" )
    set( PROJECT_LINK_LIBS "libR.so" )
    message( "previously empty, now: ${PROJECT_LINK_LIBS}" )
endif()

#include_directories("usr/local/lib/R/site-library/Rcpp/include/")
add_executable(test ../rcpp_example/Rcpp_demo.cpp)
target_link_libraries(test ${PROJECT_LINK_LIBS})
#target_link_libraries(test libR)
#target_include_directories(test PUBLIC "usr/local/lib/R/site-library/Rcpp/include/")
