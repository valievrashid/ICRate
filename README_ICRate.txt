This example demonstrates the generic NACME-route input.

No Gaussian or Firefly log file is required for this example.

Required files:
- coord.txt   : atomic labels and Cartesian coordinates
- nacme.txt   : NACME vectors for all atoms
- E.txt       : transition energy in cm^-1

Run:
ic_rate.exe

The program will read coord.txt, nacme.txt, and E.txt and write:
universal_ic_result.txt