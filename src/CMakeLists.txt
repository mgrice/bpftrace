cmake_minimum_required(VERSION 2.8.12)
project(bpftrace)

find_package(BISON REQUIRED)
find_package(FLEX REQUIRED)
find_package(LLVM REQUIRED CONFIG)

bison_target(parser parser.yy ${CMAKE_CURRENT_BINARY_DIR}/parser.tab.cc)
flex_target(lexer lexer.l ${CMAKE_CURRENT_BINARY_DIR}/lex.yy.cc)

include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

add_executable(bpftrace main.cpp driver.cpp parser.tab.cc lex.yy.cc ast.cpp printer.cpp codegen_llvm.cpp codegen_bcc.cpp map.cpp semantic_analyser.cpp)

llvm_map_components_to_libnames(llvm_libs irreader bpfcodegen mcjit)
target_link_libraries(bpftrace ${llvm_libs})