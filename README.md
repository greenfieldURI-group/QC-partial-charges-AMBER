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
The `$control` group defines the overall quantum calculation method, job type, charge, multiplicity, and coordinate system.

The system's net charge is defined by the `ICHARG` command, which has a default of zero. The default value will be implemented if `ICHARG` is not specified. 

The 
