# QC-partial-charges-AMBER
A step-by-step procedure for calculating partial atomic charges compatible with the AMBER General Force Field (GAFF) uses quantum chemical calculations with GAMESS and RESP fitting via Antechamber.

## System Setup
First, you need a PDB or XYZ file of your molecule. If the structure is unavailable, you can easily build it using molecular editing and visualization software such as Avogadro, VMD, PyMOL, etc. 
Below is an example of a biomolecule PDB file used in this tutorial.
<pre> /examples/biobinder_molecule/01-structure/BB14.pdb </pre>

## Quantum Calculation Setup
We used the open-source [GAMESS-US](https://www.msg.chem.iastate.edu/GAMESS) package for quantum calculations. 
To prepare a proper GAMESS input file, we recommend you review the input file format and available options in [GAMESS-US](https://www.msg.chem.iastate.edu/GAMESS).  
Here, we walk through a few options used for calculating partial charges. The directory below provides the GAMESS input file used in this tutorial:
<pre> /examples/biobinder_molecule/02-GAMESS-input-file/bb14-b3lyp.inp  </pre>

On the basis of [AMBER](https://ambermd.org/tutorials/basic/tutorial19/index.php) suggestions, the 6-31G* basis set is chosen, which is mentioned in the command below
<pre> $BASIS GBASIS=N31 NGAUSS=6 NDFUNC=1 $END  </pre>

The `$control` group defines the overall quantum calculation method, job type, charge, multiplicity, and coordinate system. For example:
<pre>$CONTRL SCFTYP=RHF RUNTYP=OPTIMIZE DFTTYP=B3LYP MULT=1 COORD=UNIQUE
        MOLPLT=.TRUE. MAXIT=50 $END </pre>

The system's net charge is defined by the `ICHARG` command, which has a default of zero. The default value will be implemented if `ICHARG` is not specified. 

The `MULT` keyword specifies the spin multiplicity of the electronic state. The default value is 1, which corresponds to a singlet state, meaning all electrons are paired (no unpaired electrons).
For systems with at least one unpaired electron, the user must specify the multiplicity. 

The `SCFTYP` keyword is used to specify the type of self-consistent field (SCF) wavefunction. Several options are available, but the two most common are RHF (Restricted Hartree-Fock) and UHF (Unrestricted Hartree-Fock). RHF is typically used for closed-shell systems, where all electrons are paired, and UHF is used for open-shell systems that contain unpaired electrons. The maximum number of SCF iterations is specified by `MAXIT=50`. The calculation will terminate with an error if the SCF procedure does not converge within 50 iterations. To activate the direct SCF, which means calculating integrals rather than storing them all in a memory disk, you need to use `DIRSCF=.TRUE.` in the `$SCF` group as:
<pre>$SCF DIRSCF=.TRUE. $END </pre>

The `DFTTYP` keyword performs density functional theory (DFT) calculations. The [AMBER](https://ambermd.org/tutorials/basic/tutorial19/index.php) force field recommends using `B3LYP`, a widely used hybrid functional that combines five components: Becke, Slater, and Hartree-Fock (HF) exchange (B3), along with LYP and VWN5 correlation functionals.

`RUNTYP=OPTIMIZE` is used to perform energy minimization. If you plan to calculate partial charges without optimizing the geometry, use `RUNTYP=ENERGY` instead. The `$STATPT` group controls the settings for geometry optimization:
 <pre>$STATPT OPTTOL=0.0001 NSTEP=200 PROJCT=.FALSE. $END </pre>
 where the `OPTTOL` keyword specifies the gradient convergence tolerance, the `NSTEP` keyword specifies the maximum number of steps, and `PROJCT` controls whether to project out translation and rotation from the optimization gradient.

The `COORD=UNIQUE` provides Cartesian coordinates in the `$DATA` block.

The `MOLPLT=.TRUE.` enables molecular orbital output for visualization.

The `$SYSTEM` group sets options related to memory usage and parallel computation. Please review the docs-input.txt file of the [GAMESS-US](https://www.msg.chem.iastate.edu/GAMESS) package.
<pre>$SYSTEM MWORDS=100 MEMDDI=500 PARALL=.TRUE. $END</pre>

The `$ELPOT` and `$PDC` groups control the generation of electrostatic potential (ESP) data for computing partial atomic charges. 
<pre>$ELPOT IEPOT=1 WHERE=PDC $END
$PDC PTSEL=CONNOLLY $END </pre>
`IEPOT=1` activates the calculation of the ESP on a grid, while `WHERE=PDC` specifies where to place the grid points for the ESP calculations. `PDC` stands for Potential Derived Charges, and the group determined the method of computing ESP-based atomic charges. Following the [AMBER](https://ambermd.org/tutorials/basic/tutorial19/index.php) recommendation, the `CONNOLLY` method is selected. In this approach, grid points are distributed over a set of fused van der Waals spheres using a surface generation algorithm developed by Michael Connolly.

At the end of the GAMESS input file, the `$DATA` group must be identified, which specifies the title, symmetry, and atomic coordinates.
<pre> $DATA 
Title
C1
O     8.0     1.0669000000   0.3186900000  -1.1464000000
O     8.0    -7.0195700000   1.3754900000  -0.4785000000
O     8.0     4.7814100000   1.0959300000  -0.1638200000
O     8.0     6.2342900000   2.4721300000   0.5723600000
C     6.0     0.4666300000  -0.8737300000  -0.6069900000
                  .
                  .
                  .
 $END        
</pre>
The `Title` line is a user-defined title line. The `C1` indicates no symmetry. After that, the atom name, atomic number, and Cartesian coordinate (in &Aring;) are defined at each line. 

If the simulation is converged, a `.dat` format file containing all information about the EPS calculations will be generated.
<pre>/examples/biobinder_molecule/02-GAMESS-input-file/bb14-b3lyp.dat</pre>
In the following section, we introduce our in-house C++ script that converts this data into the input files required by the RESP package.

## GAMESS output to RESP input
