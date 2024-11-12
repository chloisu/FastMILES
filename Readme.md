# FastMILES
Last modified: Niklaus M. Leuenberger, November 11, 2024

FastMILES is a fast monotonically implicit large eddy simulation for the simulation of indoor ventilation cases. It uses source terms to model heat and tracer release to circumvent the necessity for boundary layer resolution.

## Github
The code is also available via the following github repository: https://github.com/chloisu/FastMILES

## Contact 
Contact addresses: 

* Niklaus M. Leuenberger,  
Stanford University, [Department of Energy Science & Engineering](https://ese.stanford.edu/)  
Green Earth Sciences Bldg. Rm 151  
367 Panama Street, Stanford, CA 94305, U.S.A  
[niklausl@stanford.edu](mailto:niklausl@stanford.edu)


* Prof. Patrick Jenny,  
ETH Zürich, [Institute of Fluid Dynamics](http://www.ifd.mavt.ethz.ch),  
ML H 32, Sonneggstrasse 3, 
8092 Zürich, Switzerland  
[jenny@ifd.mavt.ethz.ch](mailto:jenny@ifd.mavt.ethz.ch)

## FastMILES Code for publication
This folder contains the monotonically implicit large eddy simulation code of the publication: [Leuenberger, N. M., Yang, R., Münzel, L., Verzicco, R., Lohse, D., Bourouiba, L., & Jenny, P. (2024). Fast monotonically integrated large eddy simulation solver: validation of a new scalable tool to study and optimize indoor ventilation. Flow, 4, E23. doi:10.1017/flo.2024.11](https://doi.org/10.1017/flo.2024.11).

Context: The work was done between Summer 2020 and Summer 2024. During the Covid-19 pandemic, ETH Zurich was interested in understanding the risk of airborne Covid-19 transmission in lecture halls. The [Institute of Fluid Dynamics](http://www.ifd.mavt.ethz.ch) therefore worked on the spread due to aerosols both experimentally [^sensors] and computationally (this work). At the same time, Prof. Lydia Bourouiba, an international expert in [Fluid Dynamics of Disease Transmission](https://lbourouiba.mit.edu/) from MIT was visiting the Institute. Later in 2021, after a talk at ETH by Prof. Detlef Lohse from the [Physics of Fluids department](https://pof.tnw.utwente.nl/) at the University of Twente, where he presented the Direct Numerical Simulation results [^dns], we collaborated in the development of this monotonically implicit large eddy simulation that is presented in the manuscript. 

Code: The code is directly built on top of [ParallelStencil.jl](https://github.com/omlins/ParallelStencil.jl), a "Package for writing high-level code for parallel high-performance stencil computations that can be deployed on both GPUs and CPUs." (taken from their github package description). See Omlin and Räss [^parallel_stencil], for a recent Conference Paper.

This file is organized as follows:
- [FastMILES](#fastmiles)
  - [Github](#github)
  - [Contact](#contact)
  - [FastMILES Code for publication](#fastmiles-code-for-publication)
    - [Requirements](#requirements)
      - [Julia](#julia)
      - [Python](#python)
    - [How to proceed to reproduce a certain case](#how-to-proceed-to-reproduce-a-certain-case)
    - [Code](#code)
      - [Preprocessing](#preprocessing)
        - [1 Person](#1-person)
        - [Important notice!!!](#important-notice)
        - [4 Person](#4-person)
        - [Important notice!!!](#important-notice-1)
        - [Lecture hall](#lecture-hall)
      - [Runs to Reproduce](#runs-to-reproduce)
        - [Running a simulation](#running-a-simulation)
          - [1 Person](#1-person-1)
          - [Important notcie!!!](#important-notcie)
          - [4 Person](#4-person-1)
          - [Important notcie!!!](#important-notcie-1)
          - [Lecture Hall](#lecture-hall-1)
          - [Important notcie!!!](#important-notcie-2)
    - [Data](#data)
      - [How to obtain a complete Data folder](#how-to-obtain-a-complete-data-folder)
      - [Contents](#contents)
      - [Postprocessing](#postprocessing)
  - [Authors](#authors)
  - [License](#license)
  - [References](#references)
  - [Acknowledgments](#acknowledgments)

### Requirements
#### Julia 
The simulation is written in Julia using [ParallelStencil.jl](https://github.com/omlins/ParallelStencil.jl). The following shows the output of the status command for a working package combination.
```console
(@v1.9) pkg> status
Status `C:\Users\~\.julia\environments\v1.9\Project.toml`
  [dce04be8] ArgCheck v2.3.0
⌃ [da04e1cc] MPI v0.20.14
  [15e1cf62] NPZ v0.4.3
⌃ [94395366] ParallelStencil v0.8.2
⌃ [91a5bcdd] Plots v1.39.0
```

#### Python
Python is used for preprocessing. Although not included here, we also used Python for our postprocessing and plotting scripts.

Here, we tried to include all files that get included in the files in [Preprocessing](#preprocessing). Minor modifications to the files might reduce this list considerably.
- python (3.6.15) 
- numpy (1.19.5)
- h5py  (3.1.0)
- tqdm (4.66.4) 
- matplotlib (3.3.4)
- mayavi (4.8.0.dev0) 

### How to proceed to reproduce a certain case 

To reproduce a certain simulation case from the paper, please proceed as follows:

1. Go to the [Preprocessing](#preprocessing) section and copy/generate the required input masks for your case.
2. Go to the [Runs to Reproduce](#runs-to-reproduce) section and copy the masks generated in step 1 to the desired directory.
3. Read the section [Running a simulation](#running-a-simulation) on how to run a simulation given the completed steps 1 and 2.

### Code
#### Preprocessing
This folder contains the source files necessary to generate the required inputs for the runs in: [Runs to Reproduce](#runs-to-reproduce). It has three subdirectories for the three different cases of 1 Person in the box, 4 Person in the box and the different lecture hall scenarios. 

##### 1 Person

<details>
<summary>1 Person case</summary>

Most of the required input masks for these cases are already included. Copy the *.npy files from the directory to the folder of the scenario, you would like to run (i.e. into the folder within [Runs to Reproduce](#runs-to-reproduce) with the .jl (Julia) files.) 

##### Important notice!!!  
* The *n_x = 300* masks were generated with the *setup.py* script but could unfortunately not be compared against the original masks used for the simulations in the manuscript because the latter were lost due to automatic purging of files on the $SCRATCH folder of the cluster they were run.

* You will need to rename the files after copying to the appropriate [Runs to Reproduce](#runs-to-reproduce) folder according to the include file names in the *settings.jl*.

* If you would like to generate masks with a different resolution or heat layer thickness, please change the *n_x* and *thickness* paramters in *setup.py* and then run from within *Code>Preprocessing>1Person*. This will however require a complete Data folder [How to obtain a complete Data folder](#how-to-obtain-a-complete-data-folder).

  ```console
  foo@bar:~$ python setup.py
  ```
</details>

##### 4 Person
<details>
<summary>4 Person</summary>

The required input masks for these cases are already included. Copy the *.npy files from the directory to the folder of the scenario, you would like to run (i.e. into the folder within [Runs to Reproduce](#runs-to-reproduce) with the .jl (Julia) files.) 

##### Important notice!!!
* Unfortunately, one of the exact input masks used for the 4 Person cases (i.e. the mask for the scalar concentration source term, y_mask_4Person_nx_150_d=05.npy.) was lost by the first author due to automatic purging of files on the $SCRATCH storage of the cluster. The files y_mask*.npy are still included. However, since the concentration was not reported in the paper, you should be able to reproduce the cases in the paper.

* The current version of the *setup.py* script can only generate the masks for nx = 150 and is based on the already provided solid/heat masks as inputs.

* If you wish to generate the masks for the 4 person cases yourself starting from the DNS mask for 1 person, you can use the provided *setup.py* script as a starting point. This will however require a complete Data folder [How to obtain a complete Data folder](#how-to-obtain-a-complete-data-folder). Also, you will need to modify the two functions

```python
generate_solid_and_heat_mask_from_DNS_4_People_1m
```
and 
```python
generate_solid_and_heat_mask_from_DNS_4_People_05m
```
defined in the file *Code>Preprocessing>utils>utils.py*.

* If you do use the setup.py file together with the two functions just mentioned, you will get similar *but not exactly the same* input masks as included in the directory that depend on these provided solid/heat masks as inputs. We apologize for that.
</details>


##### Lecture hall
<details>
<summary>Lecture hall case</summary>
To generate the necessary input masks for the different simulations, descend all the way into the directory, where you find a *.py* file *ml_e12*.py*. Then run these files with the working directory set to the directory with the python file.

For example for the base case of the lecture hall, run from within *Code>Preprocessing>LectureHall>base_case*

```console
foo@bar:~$ python ml_e_12.py
```

This will generate the necessary input masks as *.npy* files within this directories. You can then copy them into the corresponding running directories in [Runs to Reproduce](#runs-to-reproduce) such that the code can load the input masks for the simulation.
</details>

#### Runs to Reproduce
This folder contains the files that reproduce the simulation cases from the paper. Every folder with files like settings.jl and bouyant3D.jl are a directory for an individual simulation. To run a single simulation, please refer to the next section.

##### Running a simulation

###### 1 Person
<details>
<summary>1 Person case</summary>

After copying the required *.npy* masks (see [Preprocessing](#preprocessing)) into the source directory with the Julia *.jl* files, you can run a certain case from within that directory as follows.

  ```console
  foo@bar:~$ julia ./driver.jl
  ```

###### Important notcie!!!
* check whether all required input masks are present and have the correct name, by looking at the "settings.jl" file, where all the *.npy* files that will try to be loaded are specified at the top.

</details>

###### 4 Person

<details>
<summary>4 Person case</summary>

After copying the required *.npy* masks (see [Preprocessing](#preprocessing)) into the source directory with the Julia *.jl* files, you can run a certain case from within that directory as follows.

###### Important notcie!!!
* check whether all required input masks are present and have the correct name, by looking at the "settings.jl" file, where all the *.npy* files that will try to be loaded are specified at the top.

* The y-mask for the d = 0.5m case was generated and might not be the exact same as was used for the simulation in the paper. However, since the concentrations for 4 persons are anyways not reported in the paper, this can be ignored for reproducing the results from the paper.

```console
foo@bar:~$ julia ./driver.jl
```
</details>

###### Lecture Hall
<details>
<summary>Lecture hall case</summary>

After copying the required *.npy* masks (see [Preprocessing](#preprocessing)) into the source directory with the Julia *.jl* files, you can run a certain case from within that directory as follows.

```console
foo@bar:~$ julia ./driver.jl
```

###### Important notcie!!!
* check whether all required input masks are present and have the correct name, by looking at the "settings.jl" file, where all the *.npy* files that will try to be loaded are specified at the top.
</details>


### Data

#### How to obtain a complete Data folder
* The */Data* folder might not be complete (check the file is_complete.txt in the Data folder), when you download this repository. For the original data, please contact:
[niklausl@stanford.edu](mailto:niklausl@stanford.edu)


#### Contents
The folder contains two subfolders for the DNS_data and MILES_data. This is the data that was obtained after postprocessing of the raw data generated by the output of the runs [here](#runs-to-reproduce) and was used to generate the Figures in the manuscript [^miles].

DNS Data provided by Rui Yang, Roberto Verzicco and Detlef Lohse from the [Physics of Fluids department](https://pof.tnw.utwente.nl/) at the University of Twente.

#### Postprocessing
How to obtain the postprocessed Data contained in [/Data/](#data) from the raw simulation output is described in the manuscript [^miles].

## Authors
FastMILES was developed and implemented by Niklaus M. Leuenberger, Lukas Münzel and Patrick Jenny at the [Institute of Fluid Dynamics](http://www.ifd.mavt.ethz.ch), ETH Zurich.

## License
This project is licensed under the [MIT license](https://opensource.org/licenses/MIT).
Further, any research making use of this software should appropriately cite the references given below, in keeping with normal academic practice.

## References
[^miles]: [Leuenberger, N. M., Yang, R., Münzel, L., Verzicco, R., Lohse, D., Bourouiba, L., & Jenny, P. (2024). Fast monotonically integrated large eddy simulation solver: validation of a new scalable tool to study and optimize indoor ventilation. Flow, 4, E23. doi:10.1017/flo.2024.11]( https://doi.org/10.1017/flo.2024.11)

[^dns]: [Yang, R., Ng, C. S., Chong, K. L., Verzicco, R., & Lohse, D. (2022). Do increased flow rates in displacement ventilation always lead to better results?. Journal of Fluid Mechanics, 932, A3.](https://www.cambridge.org/core/journals/journal-of-fluid-mechanics/article/do-increased-flow-rates-in-displacement-ventilation-always-lead-to-better-results/20F2906D30538AB6391257C4F42F9304)

[^sensors]: [Rusch, A., & Rösgen, T. (2022). An Internet of Things Sensor Array for Spatially and Temporally Resolved Indoor Climate Measurements. Sensors, 22(12), 4377.](https://www.mdpi.com/1424-8220/22/12/4377)

[^parallel_stencil]: [Omlin et al., (2024). High-performance xPU Stencil Computations in Julia. The Proceedings of the JuliaCon Conferences, 6(64), 138](https://doi.org/10.21105/jcon.00138)

## Acknowledgments
* Benjamin Zoller, AFC Consulting
