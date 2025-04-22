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

### Basis sets
On the basis of [AMBER](https://ambermd.org/tutorials/basic/tutorial19/index.php) suggestions, the 6-31G* basis set is chosen, which is mentioned in the command below
<pre> $BASIS GBASIS=N31 NGAUSS=6 NDFUNC=1 $END  </pre>

### Quantum Calculation Method
The `$control` group defines the overall quantum calculation method, job type, charge, multiplicity, and coordinate system. For example:
<pre>$CONTRL SCFTYP=RHF RUNTYP=OPTIMIZE DFTTYP=B3LYP MULT=1 COORD=UNIQUE
        MOLPLT=.TRUE. MAXIT=50 $END </pre>

The system's net charge is defined by the `ICHARG` command, which has a default of zero. The default value will be implemented if `ICHARG` is not specified. 

The `MULT` keyword specifies the spin multiplicity of the electronic state. The default value is 1, which corresponds to a singlet state, meaning all electrons are paired (no unpaired electrons).
For systems with at least one unpaired electron, the user must specify the multiplicity. 

The `SCFTYP` keyword is used to specify the type of self-consistent field (SCF) wavefunction. Several options are available, but the two most common are RHF (Restricted Hartree-Fock) and UHF (Unrestricted Hartree-Fock). RHF is typically used for closed-shell systems, where all electrons are paired, and UHF is used for open-shell systems that contain unpaired electrons.

The DFTTYP keyword performs density functional theory (DFT) calculations. The [AMBER](https://ambermd.org/tutorials/basic/tutorial19/index.php) force field recommends using 'B3LYP', a widely used hybrid functional that combines five components: Becke, Slater, and Hartree-Fock (HF) exchange (B3), along with LYP and VWN5 correlation functionals.

