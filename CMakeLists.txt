CC=g++ -O0 -g -std=c++14

all: rvalue_refs

rvalue_refs: rvalue_refs.cpp
	$(CC) $< -o $@

clean:
	rm -vf *~ rvalue_refs
	
cmake_minimum_required (VERSION 3.1.0)
project(rvalue_refs)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_C_STANDARD 11)

add_executable(rvalue_refs rvalue_refs.cpp)
