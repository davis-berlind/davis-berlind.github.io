---
title: "Simulating the Effect of Network Structure on the Spread of Disease"
excerpt: "A continuous-time model for simulating the spread of an infectious disease across a random graph.<br/><img src='/images/infection-sim.png' width='500' height='250'>"
collection: projects
permalink: "/projects/infection-simulator"
---

# Simulating the Effect of Network Structure on the Spread of Disease

This simulation study was my final project for STOR-672: Simulation Modeling and Analysis, a
course I took with Dr. Mariana Olvera-Cravioto at the University of North Carolina in the Spring
of 2020. The model in this paper demonstrates how decreasing the average degree of the nodes in
a random graph (i.e. enacting a policy like social distancing) can slow the spread of disease in
a meaningful way. The graphs themselves are generated according to an Erased Configuration model.
I chose the model parameters in the simulation to reflect the conditions of the spread of 
COVID-19 through a moderately sized community. The code for generating the simulation experiments
is available [here](https://github.com/davis-berlind/infection-simulator); however, the use of
a cluster with a Slurm Workload Manager is required to conduct simulation experiments at any 
kind of meaningful scale. 

<object data="/files/STOR-672-final-project.pdf" width="1000" height="900" type='application/pdf'/>