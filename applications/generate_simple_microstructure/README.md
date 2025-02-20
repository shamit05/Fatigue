# Generate simple microstructure

  This Python script and DREAM.3D .json file will generate a simple microstructure with a 3D array of grains with some number of elements per grain. The default settings in the script will create a microstructure with 4 x 10 x 7 grains and 216 elements per grain.
  
  ## Python script
  
  To execute the script, users should navigate to the directory that contains this script and execute the following command using command prompt:
 
  ```bash
  python generate_simple_ms.py
  ```
  
  Users can also execute this script in an interactive version of Python. First, execute the "ipython" command in a command prompt window to start an interactive session:
  
  ```bash
  ipython
  ```
  
  Then execute the following commands:
  
  ```python
  import generate_simple_ms as gen_ms # Import the script
  gen_ms.main() # Execute the 'main()' function
  ```
  
  The two variables for users to change are:
  
  ```python
    # Number of elements per side for each grain, i.e., a value of 3 will create a microstructure with 3^3 = 27 elements per grain
    num_elem_side = 6
    
    # Number of desired grains in the X, Y, and Z directions
    grains_shape = np.asarray((4,10,7))
  ```  
  
  
  The variable definitions above will create a text file entitled "grains_4_10_7_elems_per_grain_216_v1.txt". This text file can then be read by the DREAM.3D .json file to create visualizations and to further manipulate the data for numerical simulation or analysis.
  
  
  ## DREAM.3D .json pipeline
  
  The .json pipeline here will read in the .txt file generated by the Python script. Users will then point to one of the available .dream3d files located in this directory (e.g., cubic_texture.dream3d, random_texture.dream3d, etc.) or modify these as desired. As a reminder, these .dream3d files and those located in the [src folder](https://github.com/prisms-center/Fatigue/tree/main/src) contain two filters: "StatsGenerator" and "Write DREAM.3D Data File". The former controls crystallographic texture, and since grains are already created, it will not control the size or shape of grains. For the .json pipeline presented here, users **must** use one of the .dream3d files in **this** folder because in these, the "StatsGenerator" filter prescribes only a single phase. In contrast, the .dream3d files from the [src folder](https://github.com/prisms-center/Fatigue/tree/main/src) may cause this .json pipeline to crash because they specify a "primary" phase and a secondary or "precipitate" phase.
  
  **NOTE**: Users must change these settings in the .json pipeline:
  
  1. The origin and resolution (i.e., size in X, Y, and Z directions) of voxels in the "Import Dx File (Feature Ids)" filter. This is optional.
  2. The number of Features (i.e., grains) in the "Create Attribute Matrix" filter. This MUST be equal to the number of grains generated by the Python script + 1. For the example here, the number must be set to 4 x 10 x 7 = 280 + 1 = 281
  3. The location of the .dream3d file for the "Read DREAM.3D Data File" filter that contains the desired crystallographic information. The "StatsGenerator" data generated **must** contain information for only a single phase.
  4. The paths to where subsequent files are stored (e.g., .csv file with feature data, .vtk file with microstructure information at each voxel, .dream3d file that contains the entire microstructure, etc.).
  
  The default settings in the Python script will create the following microstructure, show here with unique grains. As listed, there are 4, 10, and 7 grains in the X, Y, and Z directions, respectively. As a note, this microstructure is non-periodic.
  
  ![grain_IDs](https://user-images.githubusercontent.com/74416866/118996464-8e8a9800-b94d-11eb-8f4c-eacc0fc8954a.png)
  
  The same microstructure with elements displayed is shown below. As noted, there are 6 elements per side for each grain, which results in 6^3 = 216 elements per grain.
  
  ![grain_IDs_with_elements](https://user-images.githubusercontent.com/74416866/118996809-cdb8e900-b94d-11eb-8dba-0f5cf5946d81.png)
  
  This .json pipeline was executed using DREAM.3D version 6.5.141.
  
  This type of microstructure is sometimes simulated in manuscripts but may be difficult for users to quickly generate. One example of where such a microstructure has been used is the following reference (see Fig. 2):
  
  Castelluccio, G. M. & McDowell, D. L. Microstructure and mesh sensitivities of mesoscale surrogate driving force measures for transgranular fatigue cracks in polycrystals. *Mater. Sci. Eng. A* **639**, 626–639 (2015). https://doi.org/10.1016/j.msea.2015.05.048.
  

  
  ## Notes for use with the generate_microstructures.py script and PRISMS-Fatigue
  
  Once the .json pipeline creates the other microstructure files, the [generate_microstructures.py](https://github.com/prisms-center/Fatigue/blob/main/src/generate_microstructures.py) script can be used to create the necessary post-processing files for Fatigue Indicator Parameter (FIP) volume averaging. 
  
  The unique settings for the [generate_microstructures.py](https://github.com/prisms-center/Fatigue/blob/main/src/generate_microstructures.py) script to use with the microstructure files generated above are shown below.
  
  ```python
    ''' Specify directory '''
    directory = os.path.dirname(DIR_LOC) + '\\tutorial\\test_ms_gen'

    ''' Specify microstructure size and shape '''
    # Size of microstructure instantiations in millimeters, in the X, Y, and Z directions, respectively.
    # Assume here a voxel/element size of 0.0025 millimeters or 2.5 microns; NOTE: should be the same as the "Import Dx File (Feature Ids)" filter in the .json pipeline above.
    # Calculated using [4, 10, 7] x 0.0025 x 6 
    size  = np.asarray([.06,.15,.105])
    
    # Shape of microstructure instantiations (number of voxels/elements), in the X, Y, and Z directions, respectively.
    # IMPORTANT: at this point, only CUBIC voxel functionality is supported, even with a non-cubic microstructure
    # I.e., size = [.05, .1, .025] and shape = [50, 100, 25] is acceptable
    # Calculated using [4, 10, 7] x 6
    shape = np.asarray([24,60,42])
    
    # Number of microstructure instantiations to manipulate
    # Only a single microstructure was generated using the scripts above
    num_instantiations = 1
    
    ''' Specify boundary conditions '''
    # This simple microstructure is generated as non-periodic, so there is no need to shift around elements or any need for further pre-processing.
    face_bc = ['free', 'free', 'free']

    # Set this to false because we are not using DREAM.3D to generate microstrucutres in this script; we are simply manipulating the already existing microstrucutre files for subsequent FIP volume averaging.
    generate_new_microstructure_files = False
  ```
 
  For questions or comments, please contact Krzysztof (Kris) Stopka at stopka.kris@gmail.com.
