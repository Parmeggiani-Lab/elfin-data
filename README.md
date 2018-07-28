# Elfin Data

This private repository hosts raw repeat protein atomic models contributed by TJ Brunette, David Baker, and [Fabio Parmeggiani](https://github.com/parmef).

The repository should only be accessible to those who have been granted permission from either of the above authors.

The data here are used in the main [elfin](https://github.com/joy13975/elfin) project.


## Data Flow

1. Raw PDBs (`pdb_raw`) and Hub Module Metadata (`metadata`)

   > preprocess.py

2. Prepped PDBs (`pdb_prepped`)

   > create_relax_list and Rosetta's relaxation protocol

3. Relaxed PDBs (`pdb_relaxed`)
   
   > dbgen.py

4. Aligned PDBs (`pdb_aligned`) and Transformation Database (`xdb.json`)

   > batch_convert.py (pymol)

5. Aligned OBJ Models (`obj_aligned`)
   
   > OBJ Batch Preprocess (Blender)

6. Blender Module Library (`library.blend`)

Stage 2, 4, 5, and 6 are absent from this repository because they are relatively quick to produce but are massive in size.

`xdb.json` and `library.blend` are publicly hosted at [elfin-library](https://github.com/joy13975/elfin-library).