CC=g++ -O0 -g -std=c++14

all: rvalue_refs

rvalue_refs: rvalue_refs.cpp
	$(CC) $< -o $@

clean:
	rm -vf *~ rvalue_refs