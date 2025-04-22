# QC-partial-charges-AMBER
A step-by-step procedure for calculating partial atomic charges compatible with the AMBER General Force Field (GAFF) uses quantum chemical calculations with GAMESS and RESP fitting via Antechamber.

## System Setup
First, you need a PDB or XYZ file of your molecule. If the structure is unavailable, you can easily build it using molecular editing and visualization software such as Avogadro, VMD, PyMOL, etc. 
Below is an example of a biomolecule PDB file used in this tutorial.
<pre> /examples/biobinder_molecule/01-structure/BB14.pdb </pre>

## Quantum Calculation 
We used the open-source [GAMESS-US](https://www.msg.chem.iastate.edu/GAMESS) package for quantum calculations. 
To prepare a proper GAMESS input file, we recommend reviewing the input file format and available options in [GAMESS-US](https://www.msg.chem.iastate.edu/GAMESS).  
Here, we walk through a few options used for calculating partial charges. The directory below provides the GAMESS input file used in this tutorial:
<pre> /examples/biobinder_molecule/02-GAMESS-input-file/bb14-b3lyp.inp  </pre>
