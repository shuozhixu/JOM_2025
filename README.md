# Unstable stacking fault energies in refractory random alloys

## Foreword

The purpose of this project is to calculate the unstable stacking fault energies (USFEs) of 10 ternaries and 990 binaries. All alloys have a body-centered cubic (BCC) lattice. For each alloy, we need to run 20 LAMMPS simulations. Therefore, in total 20,000 LAMMPS simulations are needed.

The 10 ternaries are listed in Table 1 of [this paper](https://doi.org/10.1016/j.jallcom.2023.170556).

The 990 binaries include

- 99 binaries based on Mo<sub>_x_</sub>Nb<sub>_1-x_</sub>, where _x_ varies from 0.01 to 0.99
- other combinations of metals, including MoTa, MoV, MoW, NbTa, NbV, NbW, TaV, TaW, and VW

Please read the following journal articles to understand how the generalized stacking fault energy (GSFE) curves and their peak values (i.e., USFEs) can be calculated in BCC metals and alloys.

\[Elemental materials\]:

- Xiaowang Wang, Shuozhi Xu, Wu-Rong Jian, Xiang-Guo Li, Yanqing Su, Irene J. Beyerlein, [Generalized stacking fault energies and Peierls stresses in refractory body-centered cubic metals from machine learning-based interatomic potentials](http://dx.doi.org/10.1016/j.commatsci.2021.110364), Comput. Mater. Sci. 192 (2021) 110364

\[Alloys\]:

- Rebecca A. Romero, Shuozhi Xu, Wu-Rong Jian, Irene J. Beyerlein, C.V. Ramana, [Atomistic calculations of the local slip resistances in four refractory multi-principal element alloys](http://dx.doi.org/10.1016/j.ijplas.2021.103157), Int. J. Plast. 149 (2022) 103157
- Shuozhi Xu, Wu-Rong Jian, Irene J. Beyerlein, [Ideal simple shear strengths of two HfNbTaTi-based quinary refractory multi-principal element alloys](http://dx.doi.org/10.1063/5.0116898), APL Mater. 10 (2022) 111107

Note: Pay attention to the amount of data in your $HOME. They can build up quickly. Once the data exceeds 20 GB, you won't be able to run anything. In addition, it may be wise to figure out how to run those simulations automatically, e.g., using [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)), as opposed to manually making changes to the files.

## LAMMPS

LAMMPS on [OSCER](http://www.ou.edu/oscer.html) likely does not come with many packages. To finish this project, the [MLIP](https://mlip.skoltech.ru) package is needed.

To install LAMMPS with MLIP, follow these steps:

1. [Install MLIP](https://gitlab.com/ashapeev/mlip-2-tutorials/-/wikis/installation-tutorial)
2. [Install the LAMMPS-MLIP interface and LAMMPS](https://gitlab.com/ashapeev/interface-lammps-mlip-2)

Note: if we use sbatch files from [LAMMPSatOU](https://github.com/ANSHURAJ11/LAMMPSatOU), we may want to change the walltime (default: 12 hours) and/or number of cores (default: 16). For this project, let's use

	#SBATCH --time=48:00:00
	#SBATCH --ntasks=32

Each time we run a new type of simulation, create a new directory.

## Ternaries

### MoNbTa

#### Lattice parameter

Run a LAMMPS simulation with files `lmp_0K.in`, `fitted.mtp`, and `mlip.ini`. The first file can be found in the `ternary/lat_para/` directory in this GitHub repository. The other two files can be found in the `MTP/` directory in this GitHub repository.

Once it is finished, we will find a new file `a_E`. The first column is the ratio of the trial lattice parameter to 3.3, the second column is the trial lattice parameter itself, in units of Angstrom, the thrid column is the cohesive energy, in units of eV. If we plot a curve with the second column as the _x_ axis and the third column as the _y_ axis, the curve should look like the ones in Figure 1(a) of [this paper](http://dx.doi.org/10.1016/j.commatsci.2021.110942).

Then run `sh min.sh` to find out the trial lattice parameter corresponding to the lowest cohesive energy (i.e., the minimum on that curve), and that would be the actual lattice parameter. Specifically, we will see three numbers on the screen. The second number is the actual lattice parameter of MoNbTa.

#### GSFE

##### Plane 1

The simulation requires files 
`lmp_gsfe.in`, `fitted.mtp`, and `mlip.ini`. The first file can be found in the `ternary/gsfe/` directory in this GitHub repository.

Run the simulation. Once it is finished, we will find a new file `a_E`. The first column is the ratio of the trial lattice parameter to 3.561; the other two columns have the same meaning as the random case. Repeat the remaining steps in the random case and record the three numbers for the CoCrNi with CSRO.

##### Other planes

### Elastic constants at 0 K

Results, based on the [100]-[010]-[001] system, are in the file `CoCrNi/ela_const/0K/data.txt`. 

#### Random CoCrNi

Run the simulation with files `in.elastic`, `displace.mod`, `init.mod`, `potential.mod`, `min.CoCrNi_27nmx_27nmy_27nmz_random.dat`, and `CoCrNi.lammps.eam`.

Once it is finished, we will find an output file, `*.out`, at the end of which we will find values of C11all, C12all etc. Specifically, we will see

	Elastic Constant C11all = 309.979695007998 GPa
	Elastic Constant C22all = 310.087578803684 GPa

Since the elastic constants are in the [1-10]-[11-2]-[111] system, they should be [converted](https://github.com/shuozhixu/elastic_tensor) to those in the [100]-[010]-[001] system.

#### CoCrNi with CSRO

The simulation requires files `in.elastic`, `displace.mod`, `init.mod`, `potential.mod`, `min.CoCrNi_27nmx_27nmy_27nmz_350KMDMC.dat`, and `CoCrNi.lammps.eam`. Make one change in `init.mod`:

- Change the last number (by default 1.) of line 50 to the correct ratio identified in the prior lattice parameter calculation, i.e., the first of the three numbers we recorded for the CSRO case.

Since the elastic constants are in the [1-10]-[11-2]-[111] system, they should be [converted](https://github.com/shuozhixu/elastic_tensor) to those in the [100]-[010]-[001] system.

### Lattice parameters at 300 K

#### Random CoCrNi

Run the simulation with files `lmp_300K.in`, `min.CoCrNi_27nmx_27nmy_27nmz_random.dat`, and `CoCrNi.lammps.eam`.

Once it is finished, go to the file `log.lammps` and find the first block of data that starts with a line `Step Lx Ly Lz`. The first column of the data block starts from 0, increasing in increment of 100, and stops at 10000.

In fact, we will find that the first line of the block is

	0    271.59916    274.41197     277.1948
	
while the last line is

	10000     272.5714    275.39428    278.18707

First, calculate the lattice parameter at the last step 10000, using

(Lx'/Lx + Ly'/Ly + Lz'/Lz)/3

where Lx', Ly', and Lz' are taken at the step 10000, while Lx, Ly, and Lz are taken at step 0. Record the result.

Then repeat the equation above, but using Lx', Ly', and Lz' at steps 9900, 9800 , ..., and 9100, respectively. In total, we get ten lattice parameters. Calculate the mean of the ten numbers, and that is the ratio of the lattice parameter for random CoCrNi at 300 K to the trial lattice parameter, 3.5564 Angstrom.

We will also find a newly generated file `data.relax`, which will be used later in elastic constants calculations.

#### CoCrNi with CSRO

Repeat the steps above, except that

- Use the data file `min.CoCrNi_27nmx_27nmy_27nmz_350KMDMC.dat` instead
- Change the word `random` to `350KMDMC` in line 10 of the file `lmp_300K.in`

Record the lattice parameter, which is for CoCrNi with CSRO. The trial lattice parameter is 3.561 Angstrom.

Again, the newly generated file `data.relax` will be used later in elastic constants calculations.

### Elastic constants at 300 K

Results are in the file `CoCrNi/ela_const/300K/data.txt`. 

#### Random CoCrNi

Run the simulation with files `in.elastic`, `init.in`, `potential.in`, `output.in`, `final_output.in`, `data.relax`, and `CoCrNi.lammps.eam`. Note that the file `data.relax` is the one we got from the `Lattice parameters at 300 K - Random CoCrNi` calculation.

Once it is finished, go to the end of the output file, and we will see

	Elastic Constant C11 = 298.291596568703 GPa
	Elastic Constant C22 = 297.341418727254 GPa

which are smaller than those calculated at 0 K, as expected.

Since the elastic constants are in the [1-10]-[11-2]-[111] system, they should be [converted](https://github.com/shuozhixu/elastic_tensor) to those in the [100]-[010]-[001] system.

#### CoCrNi with CSRO

Repeat the steps above, except that

- Use the `data.relax` file from the `Lattice parameters at 300 K - CoCrNi with CSRO` calculation instead

Since the elastic constants are in the [1-10]-[11-2]-[111] system, they should be [converted](https://github.com/shuozhixu/elastic_tensor) to those in the [100]-[010]-[001] system.

### GSFE at 0 K

#### Random CoCrNi

Run the simulation with files `lmp_gsfe.in`, `data.CoCrNi_gsfe_random`, and `CoCrNi.lammps.eam`. The first file can be found in the directory `CoCrNi/gsfe/` in this GitHub repository.

Once it is finished, we will find a file `gsfe_ori`, which should contain 3001 lines, with the first one being

	0 -374943.444563279

Then run `sh gsfe_curve.in` in the terminal to generate a new file `gsfe`. Plot a curve using its first and second columns as the _x_ and _y_ axes, respectively. Bring it to our meeting for discussion.

#### CoCrNi with CSRO

Follow the procedures above, except that

- Use the data file `data.CoCrNi_gsfe_350KMDMC` instead
- Change the word `random` to `350KMDMC` in line 14 of the file `lmp_gsfe.in`
- Change the number `3.5564` to `3.561` in line 51 of the file `lmp_gsfe.in`

### Pure metal

CoCrNi contains only one pure metal, Ni, that has the same lattice as the alloy. It would be interesting to compare the temperature effect between it and the MPEA.

For CoCrNi, [Jian et al.](http://dx.doi.org/10.1016/j.actamat.2020.08.044) built all atomistc structures, and so we directly used them. For Ni, however, we need to build the atomistic structure ourselves. The first step is to install [Atomsk](https://atomsk.univ-lille.fr).

Run the atomsk script, `atomsk_Ni.sh`, which can be found in `CoCrNi/ni/` in this GitHub repository, to build a Ni structure named `data.Ni`, by

	sh atomsk_Ni.sh

Then use the data file and the same potential file to calculate its lattice parameters and elastic constants at 0 K, 300 K, 600 K, 900 K, and 1200 K. Also calculate its GSFE at 0 K.

Note: The trial lattice parameter is 3.5564 Angstrom. When calculating the lattice parameter at 0 K, in line 44 of `lmp_0K.in`, change the three numbers 54, 63, and 45, to 30, 30, and 10, respectively. That is because different cell sizes are used here.

## MoNbTa

Similar to Ni, we need to build the atomistic structures for MoNbTa ourselves.

### Random MoNbTa

Run the atomsk script, `atomsk_MoNbTa.sh`, which can be found in `MoNbTa/random/` in this GitHub repository, to build a random MoNbTa structure named `data.MoNbTa_random`.

In the data file, change the masses section to

		1   95.96000000    # Mo
		2   92.90638000    # Nb
		3   180.94788000   # Ta

Lattice parameter, elastic constants, and GSFE of random MoNbTa have been calculated. They are summarized in the file `MoNbTa/random/data_random.txt` in this GitHub repository. Most results were based on the [EAM potential](http://dx.doi.org/10.1016/j.commatsci.2021.110942) while those at 0 K were also based on the [MTP](http://dx.doi.org/10.1038/s41524-023-01046-z).

#### Warren-Cowley (WC) parameter

First, calculate the radial distribution functions (RDF) for random MoNbTa. To do that, run a LAMMPS simulation with three files `data.MoNbTa_random`, `CrMoNbTaVW_Xu2022.eam.alloy`, and `lmp_mdmc.in`. The second file can be found in [another GitHub repository](https://github.com/shuozhixu/CMS_2022). The last file is from the `MoNbTa/csro/` directory in this GitHub repository and should be modified as follows:

- Lines 3 and 4. Change the two large numbers to zero
- Linne 25. Change the data file name to `data.MoNbTa_random`

Once the simulation is finished, we will find a file `cn.out`, which contains RDF information.

Then build a new directory named `WCP_random` and move three files there: `cn.out`, `cn.sh`, and `csro.sh`. The last two files can be found in the `MoNbTa/wc/` directory in this GitHub repository.

Run

	sh cn.sh
	
Then we will find a new directory `cn` and one or more `rdf.*.dat` files in it. Then move `csro.sh` into the `cn` directory and execute it, i.e.,

	move csro.sh cn/
	cd cn/
	sh csro.sh
	
Then we will find a file named `csro.a1.dat`, which is what we need. The 2nd to 7th numbers in that file are &alpha;\_MoMo, &alpha;\_MoNb, &alpha;\_MoTa, &alpha;\_NbNb, &alpha;\_NbTa, and &alpha;\_TaTa, respectively. These are WC parameters.

#### Density functional theory

Density functional theory (DFT) calculations will be conducted using VASP to calculate the lattice parameter and GSFE of random MoNbTa.

##### Lattice parameter

All files can be found in `MoNbTa/random/dft/a0/` in this GitHub repository. Create a new directory named `a0` on OSCER and move files there. Enter that directory.

First, create `POTCAR` by

	cat POTCAR_Mo POTCAR_Nb POTCAR_Ta > POTCAR

Second, submit the job by

	sbatch vasp.batch

Once the calculation is finished, open the file `CONTCAR` and record `lx`, `ly`, and `lz` which appear in the line 3, line 4, and line 5, respectively. The lattice parameter can be calculated by

	(lx/sqrt(6)+ly/(sqrt(2)*6)+lz/sqrt(3))/3

##### GSFE

All files can be found in `MoNbTa/random/dft/gsfe/` in this GitHub repository. Create a new directory named `gsfe-5` on OSCER and move files there. Enter that directory.

First, copy `POTCAR`, which you just created, into that directory.

Second, copy `CONTCAR`, which was just generated by the lattice parameter calculation, into that directory. Then rename the file to `POSCAR_0`.

Third, edit line 14 of the file `gsfe_curve.sh`; by default, `c` equals 1, change the value of `c` to

	16021.8/(lx*lz)

where `1/(lx*lz)` is to divide the energy by area, and `16021.8` is to convert the unit from eV/angstrom<sup>2</sup> to mJ/m<sup>2</sup>. 

Fourth, build 41 subdirectories by

	sh build.sh

Fifth, submit 41 jobs by

	sh run.sh

Once all calculations are finished, generate the GSFE curve file `gsfe` by

	sh gsfe_curve.sh

The USFE is the maximum GSFE value.

Note: we have calculated only a single GSFE curve here. [A previous paper](http://dx.doi.org/10.1016/j.intermet.2020.106844) found that multiple GSFE curves need to be calculated to obtain a good mean USFE value, if the cross-sectional area within the shift plane is small, which it is in our case.

To obtain other GSFE curves on other shift planes, please change the last number (by default `5`) at the end of line 12 of `build.sh` to `3`, `4`, `6`, and `7`, respectively. Then copy all necessary files into four directories: `gsfe-3`, `gsfe-4`, `gsfe-6`, and `gsfe-7`.

In each directory, run `sh build.sh` and `sh run.sh`; then when all calculations are finished, run `sh gsfe_curve.sh`. That way, you will get four more GSFE curves, and hence four more USFE values. Calculate the mean USFF value based on the five USFE values and report the mean value in the paper.

### MoNbTa with CSRO

#### Build the CSRO structure

##### Semi-grand canonical ensemble

The first step is to determine the chemical potential difference between Mo and Nb, and that between Mo and Ta, respectively. To this end, run Monte Carlo (MC) simulations in semi-grand canonical (SGC) ensemble using `lmp_sgc.in` and `CrMoNbTaVW_Xu2022.eam.alloy`.

Once the simulation is finished, we will find a file `statistics.dat`, which should contain one line:

	-0.021 0.32 0 0.0005  0.9995

The first two numbers are the two energy differences we provided in lines 10 and 11 of `lmp_sgc.in`, while the last three numbers are the concentrations of Mo, Nb, and Ta, respectively. Since they are not close to equal-molar, modify the two numbers in lines 10 and 11 of `lmp_sgc.in`, and run the simulation again. We can make the modification in the same folder and a new line will be appended to `statistics.dat` once the new simulation is finished. Iteratively adjust the two numbers until the material is almost equal-molar. Note that it does not have to be exactly equal-molar. The procedure is similar to what is described in Section B.2 of [this paper](https://doi.org/10.1016/j.actamat.2019.12.031).

##### Variance constrained semi-grand canonical ensemble

Once the two chemical potential differences are identified, change the two chemical energy differences in lines 10 and 11 in file `lmp_vcsgc.in` to the correct values. Then run the atomsk script, `atomsk_Mo.sh` to build a Mo structure named `data.Mo`.

Next, make two changes to `data.Mo`:

- Line 4. Change the first number `1` to `3`
- Line 12 contains the atomic mass of Mo. Add two lines after it, i.e.,

		Masses
		
		1   95.96000000    # Mo
		2   92.90638000    # Nb
		3   180.94788000   # Ta
		
		Atoms # atomic

Next, run a hybrid MD/MC simulation in variance constrained semi-grand canonical (VC-SGC) ensemble using `lmp_vcsgc.in`, `data.Mo`, and `CrMoNbTaVW_Xu2022.eam.alloy`.

Once the simulation is finished, we will find a file `data.MoNbTa_CSRO`, which is the CSRO structure annealed at 300 K, and a file `cn.out`.

We can also check whether the potential energy converges to a constant. For that, plot a curve with `pe` as the _y_ axis and `step` as the _x_ axis. We can find `pe` and `step` in the log file; only use the data in the first run. The curve may look like Figure 1(a) of [this paper](https://doi.org/10.1073/pnas.1808660115), which is for CoCrNi.

#### Material properties

Use the data file `data.MoNbTa_CSRO` to calculate its lattice parameters and elastic constants at 0 K, 300 K, 600 K, 900 K, and 1200 K. Also calculate its GSFE at 0 K.

Note that the calculated elastic constants are in the [11-2]-[111]-[1-10] system, and so they should be [converted](https://github.com/shuozhixu/elastic_tensor) to those in the [100]-[010]-[001] system.

In all calculations, use the same method for CoCrNi. Remember to modify the input files accordingly and use the appropriate potential.

###### Lattice parameters

The initial trial lattice parameter is 3.135 Angstrom, but after running LAMMPS simulations, it might change. The new trial lattice parameter can be calculated by

	(lx/(10*sqrt(6.))+ly/(46*sqrt(3.)/2.)+lz/(14*sqrt(2.)))/3.
	
where `lx`, `ly`, and `lz` can be found in the data file `data.MoNbTa_CSRO`, i.e.,

	lx = xhi - xlo
	ly = yhi - ylo
	lz = zhi - zlo

In particular, when calculating the lattice parameter at 0 K, additionally change line 44 of `lmp_0K.in` to

	variable lat_para equal (lx/(10*sqrt(6.))+ly/(46*sqrt(3.)/2.)+lz/(14*sqrt(2.)))/3.

#### WC parameter

Follow the steps in the random MoNbTa case to calculate the WC parameters in the CSRO MoNbTa structure.

Eventually, use all WC parameters to make a plot similar to Figure 2(d) of [this paper](http://dx.doi.org/10.1016/j.actamat.2020.08.044).

### Pure metals

MoNbTa contains three pure metals having the same lattice as the alloy. It would be interesting to compare the temperature effect between them and the MPEA.

Lattice parameters, elastic constants, and USFEs of the three pure metals are in `Mo.txt`, `Nb.txt`, and `Ta.txt`. They were studied at 0 K, 300 K, 600 K, 900 K, and 1200 K, respectively. The only exception is that Nb becomes unstable at 1200 K so there is no data for that case.

## HfMoNbTaTi

Calculations at 0 K were performed when preparing [this paper](http://dx.doi.org/10.1063/5.0116898), although USFE data were not reported there. All data at finite temperatures are newly calculated for the current project. In all cases, simulation cells with size D (see Table II of the paper) were used.

Lattice parameters, elastic constants, and USFEs are summarized in the directory `HfMoNbTaTi` in this GitHub repository. USFEs, taken on the \{110\} plane, are in units of mJ/m<sup>2</sup>.

#### Random HfMoNbTaTi

The random material was studied at 0 K, 300 K, 600 K, 900 K, and 1200 K, respectively. Results are in `data_random.txt`.

#### HfMoNbTaTi with CSRO

Following [this paper](http://dx.doi.org/10.1063/5.0116898), four levels of CSRO were considered, with the material annealed at 300 K, 600 K, and 900 K, respectively. In what follows, let's call them 300KMDMC, 600KMDMC, 900KMDMC, respectively.

All materials were studied at 0 K, 300 K, 600 K, 900 K, and 1200 K, respectively. Results are in `data_300KMDMC.txt`, `data_600KMDMC.txt`, and `data_900KMDMC.txt`.

## HfNbTaTiZr

Calculations at 0 K were performed when preparing [this paper](http://dx.doi.org/10.1063/5.0116898), although USFE data were not reported there. All data at finite temperatures are newly calculated for the current project. In all cases, simulation cells with size D (see Table II of the paper) were used.

Lattice parameters, elastic constants, and USFEs are summarized in the directory `HfNbTaTiZr` in this GitHub repository. USFEs, taken on the \{110\} plane, are in units of mJ/m<sup>2</sup>.

#### Random HfNbTaTiZr

The random material was studied at 0 K, 300 K, 600 K, 900 K, and 1200 K, respectively. Results are in `data_random.txt`.

#### HfNbTaTiZr with CSRO

Following [this paper](http://dx.doi.org/10.1063/5.0116898), four levels of CSRO were considered, with the material annealed at 300 K, 600 K, and 900 K, respectively. In what follows, let's call them 300KMDMC, 600KMDMC, 900KMDMC, respectively.

All materials were studied at 0 K only. Results are in `data_300KMDMC.txt`, `data_600KMDMC.txt`, and `data_900KMDMC.txt`.

## References

If you use any files from this GitHub repository, please cite

- Shuozhi Xu, Wu-Rong Jian, Irene J. Beyerlein, [Ideal simple shear strengths of two HfNbTaTi-based quinary refractory multi-principal element alloys](http://dx.doi.org/10.1063/5.0116898), APL Mater. 10 (2022) 111107
- Wu-Rong Jian, Zhuocheng Xie, Shuozhi Xu, Yanqing Su, Xiaohu Yao, Irene J. Beyerlein, [Effects of lattice distortion and chemical short-range order on the mechanisms of deformation in medium entropy alloy CoCrNi](http://dx.doi.org/10.1016/j.actamat.2020.08.044), Acta Mater. 199 (2020) 352--369
- Shuozhi Xu, Emily Hwang, Wu-Rong Jian, Yanqing Su, Irene J. Beyerlein, [Atomistic calculations of the generalized stacking fault energies in two refractory multi-principal element alloys](http://dx.doi.org/10.1016/j.intermet.2020.106844), Intermetallics 124 (2020) 106844