# this target will compile all files

all: GamessToResp

GamessToResp: GAMESS_to_RESP.o
	g++ -o GamessToResp GAMESS_to_RESP.o
	
GAMESS_to_RESP.o: GAMESS_to_RESP.cpp
	g++ -c GAMESS_to_RESP.cpp
		
clean:
	rm -rf *o GamessToResp


