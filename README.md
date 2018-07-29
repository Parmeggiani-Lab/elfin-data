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

## Operating the Data Flow

First get the data processing scripts from [elfin](https://github.com/joy13975/elfin) by running:

`./get_elfinpy`

Before running any scripts, activate the virtual environment:

`. ./activate`

Notice the first period `.` followed by a space then the script `./activate`. These are needed in order to expand the environment variables set in `activate` to the current shell.

Decompress the raw PDB dataset:

`tar xvjf pdb_raw.tar.bz2`

Preprocess raw PDBs to get prepped PDBs:

`preproecss.py`

**Preprocess and relax module PDBs**
`create_relax_list > relax_list.temp`

Now, if you're on a Linux cluster **that has MPI-enabled Rosetta installed** and you have the ```sbatch``` command for your job queuing system, you can simply invoke:

```
sh relax_list.temp
```

If you are on your own computer or some other different environment, you need to modify the environment variables that specify what flavour of Rosetta and queuing system you use. For instance, on my Macbook Pro (without MPI Rosetta and no queuing system) I use:
```
local=yes variant= release=macosclangrelease sh relax_list.temp
```

 - The ```local``` variable tells the ```./scripts/Shell/relax.sh``` script whether or not to use ```sbatch```.
 - The ```variant``` variable tells which build variant (e.g. ```mpi``` or ```none``` if default) of Rosetta to use. 
 - The ```release``` variable tells which release version to use. This depends on your OS, and which build version you want to use (e.g. could be the debug version).

This relaxation can take quite a while simply because the process is computationally intensive. It is therefore strongly recommended that you do this on a compute cluster that lets you spread workload across many machines. 

**Generating xdb**

After relaxation, you should see a new PDB suffixed with ```_0001.pdb``` for each PDB in ```./resources/pdb_prepped/```. These are the relaxed versions of the original preprocessed PDBs. Now copy these into a separate folder for the next step:
```
cp_relaxed_pdbs
```

Lastly, generate the xdb by invoking: 
```
dbgen.py
```

This script aligns each pair's reference frames to the reference frame of their first single module (e.g. ```D4-D4_j1_D64``` would be superimposed onto ```D4```), producing the ```./resources/pdb_aligned/``` folder. This folder contains aligned singles and pairs that will be used to reconstruct Elfin's output structures in the final stage of design. Geometric relationships will also be calculated to produce a ```./resources/xdb.json``` "pair relationship database".