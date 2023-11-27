#default build suggestion of MPI + OPENMP with gcc on Livermore machines you might have to change the compiler name

SHELL = /bin/sh
.SUFFIXES: .cc .o

LULESH_EXEC = lulesh2.0

MPI_INC = /opt/local/include/openmpi
MPI_LIB = /opt/local/lib

SERCXX = clang++
MPICXX ?= mpicxx
CXX ?= $(SERCXX)

SOURCES2.0 = \
	lulesh.cc \
	lulesh-comm.cc \
	lulesh-viz.cc \
	lulesh-util.cc \
	lulesh-init.cc
OBJECTS2.0 = $(SOURCES2.0:.cc=.o)
OBJECTS2.0-tsan = $(SOURCES2.0:.cc=.tsan.o)
OBJECTS2.0-mpi-tsan = $(SOURCES2.0:.cc=.mpi-tsan.o)
OBJECTS2.0-mpi = $(SOURCES2.0:.cc=.mpi.o)

#Default build suggestions with OpenMP for g++
CXXFLAGS = -g -O3 -fopenmp -I. -Wall
LDFLAGS = -g -O3 -fopenmp

ifndef WITH_FLOAT
CXXFLAGS += -DUSE_DOUBLE=1
endif

#Below are reasonable default flags for a serial build
#CXXFLAGS = -g -O3 -I. -Wall
#LDFLAGS = -g -O3 

#common places you might find silo on the Livermore machines.
#SILO_INCDIR = /opt/local/include
#SILO_LIBDIR = /opt/local/lib
#SILO_INCDIR = ./silo/4.9/1.8.10.1/include
#SILO_LIBDIR = ./silo/4.9/1.8.10.1/lib

#If you do not have silo and visit you can get them at:
#silo:  https://wci.llnl.gov/codes/silo/downloads.html
#visit: https://wci.llnl.gov/codes/visit/download.html

#below is and example of how to make with silo, hdf5 to get vizulization by default all this is turned off.  All paths are Livermore specific.
#CXXFLAGS = -g -DVIZ_MESH -I${SILO_INCDIR} -Wall -Wno-pragmas
#LDFLAGS = -g -L${SILO_LIBDIR} -Wl,-rpath -Wl,${SILO_LIBDIR} -lsiloh5 -lhdf5

%.o: %.cc lulesh.h
	@echo "Building $<"
	$(CXX) -c $(CXXFLAGS) -DUSE_MPI=0 -o $@  $<

%.tsan.o: %.cc lulesh.h
	@echo "Building $<"
	$(CXX) -c $(CXXFLAGS) -fsanitize=thread -DUSE_MPI=0 -o $@  $<

%.mpi-tsan.o: %.cc lulesh.h
	@echo "Building $<"
	MPICH_CXX=$(CXX) $(MPICXX) -c $(CXXFLAGS) -fsanitize=thread -DUSE_MPI=1 -o $@  $<

%.mpi.o: %.cc lulesh.h
	@echo "Building $<"
	$(MPICXX) -c $(CXXFLAGS) -DUSE_MPI=1 -o $@  $<

all: $(LULESH_EXEC) $(LULESH_EXEC)-mpi $(LULESH_EXEC)-tsan

$(LULESH_EXEC): $(OBJECTS2.0)
	@echo "Linking"
	$(CXX) $(OBJECTS2.0) $(LDFLAGS) -lm -o $@

$(LULESH_EXEC)-mpi: $(OBJECTS2.0-mpi)
	@echo "Linking"
	$(MPICXX) $(OBJECTS2.0-mpi) $(LDFLAGS) -lm -o $@

$(LULESH_EXEC)-tsan: $(OBJECTS2.0-tsan)
	@echo "Linking"
	$(CXX) $(OBJECTS2.0-tsan) $(LDFLAGS) -fsanitize=thread -lm -o $@

$(LULESH_EXEC)-mpi-tsan: $(OBJECTS2.0-mpi-tsan)
	@echo "Linking"
	$(MPICXX) $(OBJECTS2.0-mpi-tsan) $(LDFLAGS) -fsanitize=thread -lm -o $@

$(LULESH_EXEC)-mpi-tsan2: $(OBJECTS2.0-mpi-tsan)
	@echo "Linking"
	$(MPICXX) -Wl,--whole-archive ${MUST_ROOT}/lib/libonReportLoader.a -Wl,--no-whole-archive $(OBJECTS2.0-mpi-tsan) $(LDFLAGS) -fsanitize=thread -lm -o $@

clean:
	/bin/rm -f *.o *~ $(OBJECTS) $(LULESH_EXEC)*
	/bin/rm -rf *.dSYM

tar: clean
	cd .. ; tar cvf lulesh-2.0.tar LULESH-2.0 ; mv lulesh-2.0.tar LULESH-2.0

