# Hands-On 2
## Prepare environment
* `module use /pc2/groups/hpc-prf-nhrgs/modules`
* `module load MUST`
* `export MPICH_CXX=clang++`
* `export TSAN_OPTIONS='ignore_noninstrumented_modules=1'`

## Build Application
* `MPICXX=mpigxx make -j4`

## Test for DataRace with Archer
* `OMP_NUM_THREADS=2 ./lulesh2.0-tsan -s 4 -i 2`

Look into the code and try to understand the data race (do not fix them yet)

### Test for DataRace in MUST
* `MPICXX=mpigxx make -j4 lulesh2.0-mpi-tsan`
* `OMP_NUM_THREADS=2 mustrun -n 8 --must:hybrid ./lulesh2.0-mpi-tsan -s 4 -i 2`

All TSan output goes to stderr

// Doesn't currently work on cosma
//
// ### Test for DataRace in MUST report
// * `MPICXX=mpigxx make -j4 lulesh2.0-mpi-tsan2`
// * `OMP_NUM_THREADS=2 mustrun -n 8 --must:hybrid --must:tsan ./lulesh2.0-mpi-tsan2 -s 4 -i 2`
// 
// TSan output in MUST_Output.html

## Type checking in MUST
Recompile Lulesh with TypeART:
* `MPICXX=typeart-mpic++ make -j4 -B lulesh2.0-mpi`
Execute with TypeART analysis:
* `OMP_NUM_THREADS=1 mustrun -n 8 --must:typeart --must:output stdout ./lulesh2.0-mpi -i 2`



## References
* MUST: https://itc.rwth-aachen.de/must
* Archer: https://github.com/llvm/llvm-project/tree/main/openmp/tools/archer
* TypeART: https://github.com/tudasc/TypeART