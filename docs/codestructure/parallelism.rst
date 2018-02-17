########################
Parallel design in DFTB+
########################

OpenMP
------

Shared memory parallelism is used for a number of loops in the code, typically
over atoms, but also large lists (lattice points for periodic systems, atomic
neighbours, etc.)

In general, parallel do loops should have a default of SHARED, with individual
variables marked as private, for example from `coulomb.F90`::
  
  !$OMP PARALLEL DO&
  !$OMP& DEFAULT(SHARED) PRIVATE(iAt1, ii, iAt2, vect, dist) SCHEDULE(RUNTIME)
  do jj = 1, size(invRMat, dim=2)
    iAt1 = jj
    do ii = jj + 1, size(invRMat, dim=1)
      iAt2 = ii
      vect(:) = coord(:,iAt1) - coord(:,iAt2)
      dist = sqrt(sum(vect**2))
      invRMat(ii, jj) = 1.0_dp / dist
    end do
  end do
  !$OMP END PARALLEL DO

In most places in the code, the `RUNTIME` scheduling is used, but this is
not a required choice.

MPI parallelism
---------------

MPI
~~~

BLACS
~~~~~

ScaLAPACK
~~~~~~~~~

Hybrid MPI-OpenMP
-----------------
