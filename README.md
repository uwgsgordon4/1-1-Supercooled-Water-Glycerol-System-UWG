# System Preparation and All-Atom Molecular Dynamics Simulation of Water-Glycerol Mixtures at Supercooled Temperatures Using LUNAR and LAMMPS  
  
**Authors:** Stephaine Gordon,  John Hansen,  Ana Stanescu
**Affiliations:**  
- School of Computing, Analytics, and Modeling, University of West Georgia, USA  
- School of Field Investigations and Experimental Sciences, University of West Georgia, USA

**Conference:** Proc. of the 2026 Annual Modeling and Simulation Conference (ANNSIM’26), University of Central Florida, Orlando, FL, USA  
**DOI / Publisher:** Society for Modeling & Simulation International (SCS), 2026  

## Abstract

This work presents a reproducible all-atom molecular dynamics model of a 1:1 water-glycerol mixture under isothermal-isobaric conditions at 195 K, including initialization, equilibration, and a production protocol suitable for downstream structural and dynamical analyzes. The atomistic model for water and glycerol was generated using ATB topology and assembled into a combined system of 2,000 molecules using the LUNAR molecular builder. Molecular dynamics simulations of the resulting 17,000-atom system were performed with full bonded and nonbonded interactions defined using Class II force field terms in LAMMPS, utilizing parallel computation. This paper focuses on the computational methodology, system setup, simulation workflow, and verification steps involved in preparing the system for subsequent analysis of thermodynamic and structural properties. 

---  
## Introduction  
  
Molecular dynamics (MD) simulation is a widely used approach for studying the structural and thermodynamic behavior of complex mixtures. Water–glycerol systems are particularly important due to their relevance in cryopreservation, glass formation, and biomolecular stabilization. Understanding their behavior at supercooled temperatures requires atomistic simulations capable of accurately capturing hydrogen-bonding networks and dynamical properties such as viscosity, relaxation, and transport, which are central to explaining the anomalous behavior of these mixtures.

Prior MD studies have characterized water–glycerol mixtures across wide concentrations using diverse force fields at ambient or mildly supercooled temperatures. Akinkunmi et al. [1] benchmarked two all-atom glycerol models (Reiling/CHARMM22, Blieck–Chelli/AMBER) with rigid TIP3P or TIP4P/2005 water over full glycerol concentration range Xg = 0 − 1 (including pure water and pure glycerol) at T∈ [210 K–460 K], emphasizing equilibrium properties like self-diffusion; Požar and Lovrinčević [2] used GROMOS 54A7 glycerol and SPC/E water at 298 K and showed glycerol-induced hydrogen-bond strengthening and slowed transport across Xg = 0.1 − 0.9. Neither reaches the deeply supercooled regime (e.g., our target T=195 K).

Constructing large, reproducible all-atom simulation systems remains a practical challenge because the available ecosystem of setup and simulation tools is highly fragmented, with substantial variability in workflow conventions, file formats, appropriate force-field selection, and software and hardware requirements. This makes setup laborious, particularly when the primary scientific interest lies in the downstream analysis of the phenomenon under study rather than in the technical details of MD system construction.

The main contribution of this work is the development of a reproducible workflow for constructing and simulating atomistic water–glycerol mixtures with LUNAR and LAMMPS, thereby establishing a transparent basis for structural and dynamical analyses. This study further demonstrates the workflow through simulation of a 2,000-molecule equimolar mixture at 195 K and its preliminary structural validation using radial distribution functions (RDFs) and coordination numbers.

---  
## Simulation Methodology  
  
Our simulated system consists of 1,000 water molecules and 1,000 glycerol molecules (Xg = 0.5), for a total of 17,000 atoms. Molecular structures were obtained from the Automated Topology Builder (ATB) and Repository (v3.0) [3]. LUNAR was used to convert the ATB topologies to LAMMPS-compatible data files and to assemble the periodic simulation box [4]. Simulations were performed using LAMMPS [5]. Long-range electrostatics used particle–particle particle–mesh (PPPM) at an accuracy of 1.0×10−4. Lennard-Jones interactions were truncated at 10 Å with CLASS2 atom types and tail corrections. Bonded interactions (bonds, angles, dihedrals, impropers) were described with the CLASS2 force field [6], providing a flexible molecular description suitable for analyzing hydrogen-bond network structure.

The system was simulated in the NPT ensemble (T = 195 K and P = 1 atm) using a Nosé-Hoover thermostat/barostat, with a 1 fs timestep for numerical stability of the fully flexible all-atom model. Equilibration proceeded in two 1 ns stages (NVT then NPT), followed by a 2 ns production run. The entire 4 ns simulation required ≈ 22 hours wall-clock time on an NVIDIA Tesla T4 GPU while using a single CPU core from an AMD EPYC 7V12 processor. Performance reported by LAMMPS was 4.251 ns/day (5.646 hours/ns), corresponding to 49.196 timesteps/s and 836.334 katom-step/s, using 1 MPI task with 1 OpenMP thread and achieving 99.3% CPU utilization. The trajectory output was written in compressed format and produced a file of approximately 1.3 GB. Runtime scales with system size and total simulation length. Shorter equilibration stages typically complete faster than production runs.
  
---  
## Results  

The thermodynamic stability of the simulated system was assessed by monitoring the total energy and temperature, as shown in [Fig. 1a](https://github.com/uwgsgordon4/1-1-Supercooled-Water-Glycerol-System-UWG/blob/main/Analysis/Figures/temp_energy_vs_time.png). Panel (a) presents the corresponding trajectories during the equilibration (blue) and production (orange) phases. The system equilibrates rapidly and remains stable during production, with both observables fluctuating around steady mean values consistent with the target temperature of 195 K, indicating numerical stability under the chosen simulation conditions. 

The radial distribution function g(r) quantifies the local number density at a distance r from a reference site relative to the bulk density expected for a spatially uniform, uncorrelated fluid at the same overall density. The reported g(r) values therefore represent time-averaged short-range structural correlations over the production trajectory. The analysis is site-specific, with water oxygen atoms denoted as Ow and glycerol oxygen atoms treated either collectively as Og or separately as central (OgA) and terminal (OgB) hydroxyl sites to resolve possible differences in local coordination environments.

To characterize the mixture structure, oxygen-oxygen RDFs were computed for Ow–Ow, Og–Og, Ow–OgA, and Ow–OgB pairs, as shown in Fig. 1b. The Ow–Ow RDF exhibits a pronounced first peak near 2.8 Å, consistent with nearest-neighbor hydrogen-bonded water oxygens and the short-range ordering characteristic of liquid water. In contrast, the Og–Og RDF is broader, indicating a more heterogeneous and less spatially specific local environment for glycerol oxygens, consistent with the larger size and conformational flexibility of glycerol molecules. The cross Ow–OgA and Ow–OgB RDFs also show clear first-shell peaks, indicating that water frequently occupies the first coordination shell of glycerol hydroxyl groups and that water-glycerol contacts contribute  substantially to the mixed hydrogen-bond network. Because these cross peaks are lower than the Ow–Ow first peak, the local water-glycerol coordination appears somewhat less ordered than the water-water coordination environment.

Coordination numbers, calculated by integrating the RDFs up to their first minima, quantify the average population of the first coordination shell and thus provide a more direct measure of local packing. Relative to the Ow–Ow coordination, the Ow–Og coordination supports a mixed first-shell environment in which some water-water contacts are replaced by water-glycerol contacts. Such behavior is consistent with literature reports showing that glycerol perturbs the tetrahedral organization of water and restructures the hydrogen-bond network in glycerol-water mixtures [2, 7]. The coordination curves are shown in [Fig. 1b](https://github.com/uwgsgordon4/1-1-Supercooled-Water-Glycerol-System-UWG/blob/main/Analysis/Figures/rdf_coordination_combined.png). Together, these observations provide preliminary structural validation of the simulation setup and indicate that the mixture displays physically plausible local ordering under the simulated supercooled conditions.

---  
## Conclusion and Future Work

This work establishes a reproducible all-atom molecular dynamics workflow for constructing, equilibrating, and simulating an equimolar water-glycerol mixture at 195 K using LUNAR and LAMMPS. The resulting 2,000-molecule system remained numerically stable under the chosen simulation protocol and exhibited physically plausible local structure based on the computed oxygen-oxygen radial distribution functions and coordination curves. In particular, the RDF analysis supports the presence of strong short-range water-water ordering together with substantial water-glycerol first-shell coordination, consistent with a mixed hydrogen-
bond network under supercooled conditions.

The present model also provides a flexible molecular description suitable for analyzing hydrogen-bond network structure and reorganization. Future work will extend the current analysis to include explicit hydrogen-bond network characterization, self-diffusion coefficients obtained from mean squared displacement, and additional structural order parameters. However, explicit proton hopping cannot be described within a fixed-topology classical force field and therefore requires a reactive or otherwise bond-rearranging potential. Accordingly, the simulation framework will next be extended to reactive proton-transfer modeling using machine-learned many-body potentials developed within the DeePMD framework [8]. These developments will support future efforts to connect atomistic proton-transfer dynamics with experimental observables, including photochemically triggered proton release and geminate and nongeminate recombination in supercooled glycerol-water mixtures.

## Acknowledgments

The authors thank Edwin Rudolph, Innovation & Operations Manager, for assistance with computational infrastructure.

## Code and Figures  
  
All LAMMPS input files are located in **`Input files`**,  analysis scripts are in **`Analysis`**, and figures are located in the **`Analysis/Figures`** directory.  
  
---
## References  
  
1. F. O. Akinkunmi, D. A. Jahn, and N. Giovambattista, J. Phys. Chem. B, 119, 6250–6261 (2015).
2. M. Požar and B. Lovrinčević, Soft Matter, 2024.
3. M. Stroet et al., J. Chem. Theory Comput., 14, 5834–5845 (2018).
4. J. Kemppainen et al., J. Chem. Inf. Model., 64, 5108–5126 (2024).
5. S. Plimpton, J. Comput. Phys., 117, 1–19 (1995).
6. H. Sun, J. Phys. Chem. B, 102, 7338–7364 (1998).
7. J. J. Towey and L. Dougan, J. Phys. Chem. B, 116, 1633–1641 (2012).
8. J. Zeng et al., J. Chem. Theory Comput., 21, 4375–4385 (2025).
