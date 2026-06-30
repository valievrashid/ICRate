# ICRate
ICRate - Universal IC/ISC Rate Calculator
=========================================

Author: Rashid R. Valiev
Distribution: compiled Windows executable / proprietary research tool

Overview
--------
ICRate is a post-processing utility for estimating internal-conversion rate constants using X-H bond contributions. The program is designed for photophysical applications where X-H bonds (X = C, N, O, etc.) act as acceptors of electronic excitation energy.

The method is intended for internal conversion (IC) calculations. The theoretical background is described in:

1. R. R. Valiev, R. T. Nasibullin, V. N. Cherepanov, A. Kurtsevich, D. Sundholm and T. Kurtén,
   "Fast estimation of the internal conversion rate constant in photophysical applications",
   Phys. Chem. Chem. Phys., 2021, 23, 6344-6348.
   DOI: 10.1039/D1CP00257K
   https://pubs.rsc.org/en/content/articlelanding/2021/cp/d1cp00257k

2. R. R. Valiev, B. S. Merzlikin, R. T. Nasibullin, D. Sundholm and T. Kurtén,
   "The role of X-H bonds (X = C, N and O) in internal conversion processes: dibenzoterrylene as an example",
   Phys. Chem. Chem. Phys., 2025, 27, 16853-16864.
   DOI: 10.1039/D5CP01603G
   https://pubs.rsc.org/en/content/articlelanding/2025/cp/d5cp01603g

The program contains two calculation/input blocks:

Block 1: Direct quantum-chemistry output parser
------------------------------------------------
This block reads electronic-structure output files directly and extracts:

- molecular geometry,
- X-H bonds,
- excited-state energies,
- CI/SAP coefficients,
- molecular orbital coefficients,
- per-bond P_CI values,
- IC rates for excited-state -> ground-state and excited-state -> excited-state transitions.

Currently supported direct parsers:

1. Gaussian TDDFT log/out files
2. Firefly TDDFT log/out files

Important: the direct parser is implemented for TDDFT-type outputs only. Other methods are not supported in this release.

Gaussian requirements
---------------------
For Gaussian calculations, the output must contain the geometry, TDDFT excited-state section, and full molecular-orbital coefficient table. Use Gaussian TDDFT with the following output tags:

    pop=full gfinput gfprint

A typical route section should contain TDDFT and these print options, for example:

    #p TD(NStates=10) B3LYP/6-31G(d) pop=full gfinput gfprint

The program searches the current folder for the newest file with extension:

    .log
    .out

and attempts to parse it as a supported quantum-chemistry output file.

Required Gaussian output sections include, for example:

    Standard orientation:
    or
    Input orientation:

    Excitation energies and oscillator strengths:

    Molecular Orbital Coefficients:

Gaussian is not included in this software. ICRate only post-processes user-provided Gaussian output files. Users are responsible for complying with their own Gaussian license.

Firefly requirements
--------------------
For Firefly TDDFT output, the program expects sections such as:

    ATOM      ATOMIC                      COORDINATES (BOHR)
    FINAL ENERGY IS
    SAP COEFFICENT
    EIGENVECTORS

Coordinates in Firefly output are converted from Bohr to Angstrom internally.

Not supported as direct parsers in this release
-----------------------------------------------
The following direct output parsers are not implemented in this release:

- GAMESS / PC GAMESS direct output parser
- ORCA direct output parser
- Turbomole direct output parser
- Q-Chem direct output parser
- direct CASSCF output parser

Block 2: Generic NACME input
----------------------------
This block does not require Gaussian or Firefly log files. It reads only three simple text files:

    coord.txt
    nacme.txt
    E.txt

Use this block when the full NACME vectors are already available from any external program or workflow.

coord.txt format:

    AtomLabel   x   y   z

Example:

    C  0.000000  0.000000  0.000000
    H  0.000000  0.000000  1.089000

nacme.txt format:

    dx   dy   dz

There must be one NACME vector line per atom, in the same atom order as coord.txt.

E.txt format:

    energy_gap_in_cm-1

Example:

    18537.36

If coord.txt, nacme.txt, and E.txt are present, the program calculates the NACME-route IC rate and P_NACME values for all detected X-H bonds.

Combined mode
-------------
If both a supported .log/.out file and the NACME files are present in the same folder, the program performs both calculations and prints a comparison for S1->S0:

    CI vs NACME

If only NACME calculations are desired, run the executable in a folder containing coord.txt, nacme.txt, and E.txt, but no .log/.out files.

Rate cap
--------
For numerical and physical stability, the final reported rate constant is capped at:

    k_max = 5.0e13 s^-1

If the calculated value exceeds this limit, the reported value is set to 5.0e13 s^-1.

Output
------
The program writes:

    universal_ic_result.txt

The report contains:

- detected X-H bonds,
- transition energies in cm^-1,
- rate constants in s^-1,
- P_CI values for direct CI-route calculations,
- P_NACME values for generic NACME-route calculations,
- CI/NACME comparison when both inputs are present.

How to run
----------
Place the executable in the same folder as the input files and run it from the command line:

    ic_rate.exe

The output file universal_ic_result.txt will be created in the same folder.

Windows note
------------
This executable may be unsigned. Windows Defender SmartScreen may show a warning for new or unsigned applications. This does not necessarily mean that the file is malicious; it means that the executable has not yet built sufficient Microsoft reputation or has not been code-signed.

Disclaimer
----------
This software is a research tool. The author provides no warranty of correctness, fitness for a particular purpose, or compatibility with all quantum-chemistry output variants. Users should validate results for their own systems.
