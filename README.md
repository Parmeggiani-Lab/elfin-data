# elfin-data

This private repository hosts raw repeat protein atomic models contributed by TJ Brunette, David Baker, and [Fabio Parmeggiani](https://github.com/parmef). Further models were collected from various publications and were slightly modified by Leon Obendorf.

The repository should only be accessible to those who have been granted permission from either of the above authors.

The data here are used in the main [elfin](https://github.com/Parmeggiani-Lab/elfin) project.

Setup:
```
./setup
```

## Data Flow

First, activate environment:
```
. ./activate
```

Then copy your `pdb_raw` folder containing respective subfolders into `elfin-data`.

1. Create `pdb_prepped` with raw PDBs (`pdb_raw`) and Hub Module Metadata (`metadata`)

```
preprocess.py # produces a `pdb_prepped` in resources
```

2. Create `pdb_aligned` and `xdb.json` using prepped PDBs (`pdb_prepped`)

```
dbgen.py # creates `pdb_aligned` and `xdb.json`
```

This script aligns each pair's reference frames to the reference frame of their first single module (e.g. ```D4-D4_j1_D64``` would be superimposed onto ```D4```), producing the ```./resources/pdb_aligned/``` folder. This folder contains aligned singles and pairs that will be used to reconstruct Elfin's output structures in the final stage of design. Geometric relationships will also be calculated to produce a ```./resources/xdb.json``` "pair relationship database".


4. Create `obj_aligned` using aligned PDBs (`pdb_aligned`) and Transformation Database (`xdb.json`)

Run this in pymol:

```
import sys
sys.path.append('DIRECTORY TO elfin-data')
cd 'directory to elfin-data'

run pymol_scripts/extensions/batch_convert.py

batch_convert_modules resources/pdb_aligned, ressources/obj_aligned, obj

```
5. Create `library.blend` with aligned OBJ Models (`obj_aligned`)

   1. Install [elfin-ui](https://github.com/Parmeggiani-Lab/elfin-ui)
   2. In the left-hand-side panel, set the Process source folder to the folder that contains `singles`, `doubles`, `hubs` folders, which in turn contain the respective .obj files.
   3. Set the destination path for the output library file and name it `library.blend`.
   4. Hit `Batch process & export`. -> This may freeze your computer for halve an hour (my experience).
   5. open the library.blend
   6. make sure al objects are NOT ACTIVE (click on the small eye in the inspector) -> only then you can avoid a strange error that is happening when adding the first module in a scene!

Files produces by stages 2, 4, 5, and 6 are absent from this repository because they are either quick to generate or are too massive in size.

`xdb.json` and `library.blend` are hosted at different libraries in the Parmeggiani Lab Github [Protein Design Lab](https://github.com/Parmeggiani-Lab).
