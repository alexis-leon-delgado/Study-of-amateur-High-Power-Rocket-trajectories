# Bachelor Thesis: Study of High Power Rocket trajectories

![image](https://user-images.githubusercontent.com/71126768/126407189-e1ecf091-a7f3-46a1-adf2-1b798c4f33d1.png)

## Overview
**Rocket Dynamics: Ares Simulator** is a Python-based, 3-Degree-of-Freedom (3-DOF) flight dynamics and trajectory simulator specifically engineered for solid-propellant High Power rockets. Originally migrated and heavily refactored from a legacy MATLAB space launcher framework, this modular simulator is capable of predicting ascent trajectories, aerodynamic forces, and parachute descent paths, culminating in statistical splash-down boundary evaluations using Monte Carlo methods.

## Abstract
High Power rockets are suborbital vehicles with vast applications in both professional and academic sectors, ranging from atmospheric sounding to the participation in rocketry competitions. Due to their ballistic nature, the launch of these vehicles incurs unquestionable security risks that must be quantified. In order to study their flight performance, the trajectory and splash-down area must be simulated, analysed and validated. This thesis aims to develop a rocket trajectory simulator conceived to study the flight mechanics of solid-propellant amateur High Power rockets. The simulator is designed to enable different configuration possibilities, including a wide range of equations, atmosphere, drag and propulsion models. The description and derivation of the physical and mathematical models used are presented to characterise the rocket suborbital motion. These models have been implemented in the simulator, whose development is the continuation of the MATLAB-originally-implemented Rocket Dynamics software, suited only for space launcher simulations.

The code has been translated to Python and restructured to enable the treatment of amateur High Power rocket trajectories. The addition of new features has comprised the creation of a High Power solid propulsion submodule and the implementation of the equations of motion for each flight phase (launch rail motion, ascent and parachute descent), as well as their triggering flight events. Different drag models have been tested to select the most suitable one and a deep upgrade of the configuration processing scripts has been performed. All has been wrapped up in a simulation launcher suited for High Power Rocketry flight. The code has been thoroughly validated by comparing its performance for two test rockets with an open-source simulator. For one of the test cases, it has been possible to compare the software results with experimental data gathered from a real High Power Rocket launch. Overall, it has been proven that the performance of Rocket Dynamics meets the accuracy of other available simulators. Finally, a Monte Carlo simulator has been implemented in order to study the behaviour of the next UPC Space Program’s rocket, Phobos. In view of its upcoming participation in the European Rocketry Challenge 2022 competition, this data will be used to estimate the splash-down zone and confirm that the rocket’s behaviour meets the design parameters.

## Key Features
### 1. Flight Mechanics & Physics Modeling
* **Reference Frames:** Formulates equations of motion within the East-North-Up (ENU) coordinate frame, mapping transformations from ECI and ECEF systems.
* **Flight Phases:** Implements an event-driven state machine managing three distinct phases:
  * **Launch Rail Motion:** Incorporates dynamic equilibrium conditions, computing launch rail friction and precise off-rail velocity.
  * **Ascent & Free Flight:** Models powered ascent and coasting up to apogee.
  * **Parachute Descent:** Models aerodynamic deceleration utilizing user-defined recovery devices (drogues and main parachutes).
* **Atmospheric & Aerodynamic Models:**
  * Integrates the **U.S. Standard Atmosphere (USSA)** model for accurate thermodynamic property computation across altitudes.
  * Utilizes the **Henderson drag model** to compute dynamic drag coefficients across continuum, slip, transition, and molecular flow regimes up to Mach 6.
* **Aspherical Gravity:** Models gravitational acceleration using an aspherical Earth representation (WGS84 ellipsoid).

### 2. Propulsion Data Processing
* **Amateur Propulsion Submodule:** Features a custom parser designed to read standard **RASP (`.eng`)** motor files.
* Automatically processes and interpolates raw motor data to compute time-variable parameters including mass flow, total impulse, and thrust curves to match the numerical integration time steps.

### 3. Numerical Integration (`pyRKIntegrator`)
* Replaces legacy C++ solvers with a native, highly optimized Python integration module.
* Supports multiple high-order Runge-Kutta schemes tailored for stiff and non-stiff aerospace dynamic equations:
  * Bogacki-Shampine 2(3)
  * Fehlberg 4(5)
  * Cash-Karp 4(5)
  * Dormand-Prince 4-5 & 7(8)
  * Calvo 5(6)
  * Curtis 8(10)
  * Hiroshi 9(12)

### 4. Stochastic Analysis (Monte Carlo Wrapper)
* Designed for launch safety and dispersion analysis. The simulator executes large-scale (e.g., 1,750+ iterations) Monte Carlo studies.
* Injects multi-variable normal distribution noise into stochastic parameters (thrust, dry mass, component geometry, launch azimuth/elevation, recovery drag coefficients).
* Computes splash-down boundary ellipses (1σ, 2σ, 3σ confidence intervals) utilizing linear least squares regression on bivariate normal distributions.

## Architecture & Repository Structure
The software architecture follows a highly modular structure to decouple physical models, core processing, and numerical integration routines. Following the complete restructuring of the legacy framework, the repository is organized into the following primary modules:

### Directory Breakdown
* **/Atmosphere:** Contains atmospheric thermodynamic models sorted by planet. Includes the 1976 U.S. Standard Atmosphere (`Earth.py/atmosUSSA`), NASA Glenn (`atmosGlenn`), and generic exponential models.
* **/Config:** Stores the `.ini` initialization files that define the simulation inputs (Planet, Launch Site geography, and Launcher component parameters/sequences).
* **/Core:** Contains the core functions required to process the simulation data structures.
* **/Forces:** Houses aerodynamic drag coefficient models, specifically the `dragHenderson` (optimal for predicting various flow regimes for amateur rockets) and `dragTewari` functions.
* **/Gravity:** Computes gravitational acceleration vectors using either an `Aspherical.py` (WGS84 ellipsoid) or `Geopotential.py` (spherical) Earth model.
* **/Models:** Contains the core equations of motion and event-driven state transitions tailored for specific flight phases (e.g., `ENU_AM_LR` for launch rail motion, and `ENU_AM_Ascent_FreeFlight_Descent`).
* **/Propulsion:** Manages thrust and mass flow computation (`Thrust.py`, `MassFlow.py`). Features the **Amateur Propulsion** submodule, utilizing the `getMotorData` class to parse standard RASP `.eng` motor files.
* **/pyRKIntegrator:** The custom Python numerical Ordinary Differential Equation (ODE) integrator featuring eight distinct Runge-Kutta schemes (e.g., Dormand-Prince 4(5), Cash-Karp 4(5), Hiroshi 9(12)).
* **/GUI & /Tools:** Contains the Graphical User Interfaces, simulation results viewers, and auxiliary tools (such as equations of motion symbolic demonstrators).
* **/MonteCarlo:** Dedicated module and database for executing stochastic simulation runs and storing the statistical dispersion data.

---

### Simulation Execution Flow (`RunRocketDynamics_Ares.py`)

The primary execution script for an amateur High Power rocket simulation operates through a sequential, event-driven integration loop. The architecture of the execution engine follows this explicit algorithmic sequence:

**1. Configuration & Data Ingestion**
* **1.1** Reads the specified launcher configuration file (`args.configuration`).
* **1.2** Retrieves initial flight conditions, selected physical models (drag, gravity, atmosphere), planet data, and ODE solver options.

**2. Flight Sequences Integration Loop**
*For each defined flight sequence (Launch Rail $\rightarrow$ Ascent $\rightarrow$ Coasting $\rightarrow$ Descent):*
* **2.1** Retrieves the specific equations of motion model and the trigger event functions (e.g., `EventApogee`, `EventStopAtGround`) for the current phase.
* **2.2** Processes sequence boundary times based on relative motor burnout or predefined event conditions.
* **2.3** Initializes the starting time and the 7-variable state vector (velocity, flight path angle, azimuth, mass, altitude, longitude, latitude).
* **2.4** Executes the integration launcher (`pyRKIntegrator`) using the configured Runge-Kutta scheme until the event function triggers a stop.
* **2.5** Saves the integrated state variables to the data structure.
* **2.6** Sets the final state vector of the current sequence as the initial condition for the subsequent sequence.

**3. Output Generation**
* Saves the raw numerical arrays, structural variables, and reader inputs as a serialized `.pkl` file.

**4. GUI & Post-Processing**
* Launches the graphical interface.
* Computes derived flight parameters (e.g., energy losses, downrange distance).
* Renders final 2D/3D trajectory plots and telemetry graphs.

## Installation & Usage
\*To access the Bachelor Thesis complete report and/or code, please contact me.

## Validation Data
The simulator has been rigorously benchmarked:
* **Computational Benchmarking:** Achieved a ~6.1% relative error in apogee predictions against the Open Rocket parametric model. 
* **Experimental Grounding:** Validated against accelerometer and flight data gathered from the real-world launch of the Ares II multi-stage rocket.

Some of the simulations results are summed in the following graphs:

![ph_1_tr](https://user-images.githubusercontent.com/71126768/126406959-a9834b10-5ee8-4320-9572-d1f6e5b4d8da.png)
![ares_3_v](https://user-images.githubusercontent.com/71126768/126406944-79438061-d948-49e1-96f1-3e676bf9fd78.png)
![MC_1_dispersion](https://user-images.githubusercontent.com/71126768/126406881-98b5c0f3-6261-4264-8852-b506508ab842.png)



## Author
**Alexis LEON I DELGADO** Aerospace Engineer  
Developed as part of a Final Bachelor's Thesis for the UPC Space Program (Ares Mission).

<p align="center">
  <img width="550" height="650" src="https://user-images.githubusercontent.com/71126768/126407093-800d979f-f07b-4021-8d7b-ce77ee21e30c.png">
</p>
