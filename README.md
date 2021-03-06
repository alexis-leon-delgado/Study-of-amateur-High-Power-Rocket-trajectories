# Study-of-amateur-High-Power-Rocket-trajectories

![image](https://user-images.githubusercontent.com/71126768/126407189-e1ecf091-a7f3-46a1-adf2-1b798c4f33d1.png)

High Power rockets are suborbital vehicles with vast applications in both professional and academic
sectors, ranging from atmospheric sounding to the participation in rocketry competitions. Due to their
ballistic nature, the launch of these vehicles incurs unquestionable security risks that must be quantified.
In order to study their flight performance, the trajectory and splash-down area must be simulated,
analysed and validated. This thesis aims to develop a rocket trajectory simulator conceived to study the
flight mechanics of solid-propellant amateur High Power rockets. The simulator is designed to enable
different configuration possibilities, including a wide range of equations, atmosphere, drag and propulsion
models. The description and derivation of the physical and mathematical models used are presented to
characterise the rocket suborbital motion. These models have been implemented in the simulator, whose
development is the continuation of the MATLAB-originally-implemented Rocket Dynamics software,
suited only for space launcher simulations.

The code has been translated to Python and restructured to enable the treatment of amateur High
Power rocket trajectories. The addition of new features has comprised the creation of a High Power solid
propulsion submodule and the implementation of the equations of motion for each flight phase (launch
rail motion, ascent and parachute descent), as well as their triggering flight events. Different drag models
have been tested to select the most suitable one and a deep upgrade of the configuration processing scripts
has been performed. All has been wrapped up in a simulation launcher suited for High Power Rocketry
flight. The code has been thoroughly validated by comparing its performance for two test rockets with
an open-source simulator. For one of the test cases, it has been possible to compare the software results
with experimental data gathered from a real High Power Rocket launch. Overall, it has been proven
that the performance of Rocket Dynamics meets the accuracy of other available simulators. Finally, a
Monte Carlo simulator has been implemented in order to study the behaviour of the next UPC Space
Program???s rocket, Phobos. In view of its upcoming participation in the European Rocketry Challenge
2022 competition, this data will be used to estimate the splash-down zone and confirm that the rocket???s
behaviour meets the design parameters.

Some of the simulations results are summed in the following graphs:

![ph_1_tr](https://user-images.githubusercontent.com/71126768/126406959-a9834b10-5ee8-4320-9572-d1f6e5b4d8da.png)
![ares_3_v](https://user-images.githubusercontent.com/71126768/126406944-79438061-d948-49e1-96f1-3e676bf9fd78.png)
![MC_1_dispersion](https://user-images.githubusercontent.com/71126768/126406881-98b5c0f3-6261-4264-8852-b506508ab842.png)

\*To access the Bachelor Thesis report, please contact me.
<p align="center">
  <img width="550" height="650" src="https://user-images.githubusercontent.com/71126768/126407093-800d979f-f07b-4021-8d7b-ce77ee21e30c.png">
</p>
