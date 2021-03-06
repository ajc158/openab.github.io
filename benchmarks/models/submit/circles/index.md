---
title: Models
layout: default
---

# Circles Model Overview

The circles benchmark model is a benchmark model originating from FLAME and FLAME GPU. It consists of a number of points which exert a repulsive and potentially attractive force over a limited range $$2r$$. Over a number of iterations the model will exhibit a force resolution which will ultimately result in a stable state. The model has origins in a number of biological systems and is analogous to that of cellular interactions of simple swarm systems.

The model has been used for extensive benchmarking of the FLAME and FLAME GPU frameworks and is excellent for testing message acceleration structures for spatially distributed models.

# Model Description

The position $$x$$ of a circle agent $$i$$ at a discrete time step $$t + 1$$ is given by;

$$x_{i(t+1)} = x_{i(t)} + F_{i}$$

where $$F_{i}$$ denotes the force exerted on agent $$i$$ calculated as;

$$F_{i} = \sum\limits_{i \neq j} F_{ij}^{rep}[d_{ij} < r] + F_{ij}^{att}[r <= d_{ij} < 2r]$$

The parameter $$r$$ is the homogeneous radius of the circle agent. The square Iverson bracket notation determines a condition for both the repulsive force $$F_{ij}^{rep}$$ and attractive force $$F_{ij}^{att}$$ between the agent $$i$$ and a neighbouring agent $$j$$. When the condition evaluates to true it returns a value of $$1$$ otherwise it is $$0$$. The value $$d_{ij}$$ is the distance between agent positions $$x_{i}$$ and $$x_{j}$$ given by;


$$ d_{ij} = \sqrt{ (x_{j} - x_{i})^2 } $$

The repulsive and attractive forces as defined as follows;

$$ F_{ij}^{rep} = \frac{k_{rep}(d_{ij} - 2r)(x_{j} - x_{i})} {d_{ij}} $$

$$ F_{ij}^{att} = \frac{k_{att}(d_{ij} - 2r)(x_{j} - x_{i})} {d_{ij}} $$

The parameters $$k_{rep}$$ and $$k_{att}$$ are the repulsive and attractive damping terms respectively.

After the location has been calculated, it must be clamped within the environmental bounds, as specified by the parametered $$W$$ (see below).

# Initial Conditions

The initial conditions of the circles model are a randomly positioned set of circle agents. The random distribution should be linear and the following parameters should be used to benchmark the model.

$$W$$ The width and height of the environment in which agents are placed.
$$\rho$$ The density of agents within the environment.
$$E_{dim}$$ The dimensionality of the environment (in most cases this will be $$ 2 $$ or $$ 3 $$).

$$ A_{pop} $$ The constant agent population size is then calculated according to the formula;

$$ A_{pop} = \left\lfloor{W^{E_{dim}} \rho}\right\rfloor$$ 

# Validation

There are several checks that can be carried out to ensure that the benchmark has been implemented correctly. Most significantly it should be confirmed that agent locations remain within the environmental bounds. The absence of this clamping under certain model parameters allows agent density to decrease, which artificially benefits performance. During execution, the majority of particles will move in a smooth path, whereby a greater difference between the forces will cause greater particle speeds. When $$F_{att} >= F_{rep}$$, particles will cluster to a small number of locations, and overlap tightly, with many particles aligning in multiples of $r$ distance from the environment boundaries. In contrast, scenarios where $$F_{rep} > 2F_{att}$$, particles will primarily separate with minimal overlap, moving significantly slower. It is intended that the benchmark model is able to reach a steady state over a number of iterations, however in some cases high-magnitude forces $$F_{att}$$ and $$F_{rep}$$ may instead cause the agents to vibrate. 

# Model Parameters


| Parameter | Description |
| $$ k_{rep} $$ | The repulsive damping term. Increasing this value will encourage agents to separate.|
| $$ k_{att} $$ | The attractive damping term. Increasing this term will encourage agents to attract. |
| $$ r $$ | The interaction radius of the circle agents. Increasing this value will increase the communication between agents (assuming uniform density) |
| $$ \rho $$ | The density of agents within the environment. Increasing this value will increase the communication within the model. |
| $$ W $$ | The width and height of the environment in which agents are placed. Increasing the environment size is equivalent to increasing the problem size $$ N $$ (i.e. the number of agents) given a fixed $$\rho$$. |
{:.decoratedtable}


The following suggested parameter configurations and benchmarks are suggested

| Benchmark Name | Description | Parameter Values |
| Problem scale | This benchmark will test the simulators ability to handle greater problem size. | $$k_{rep}=1\times10^{-5}$$, $$k_{att}=1\times10^{-5}$$, $$\rho$$ and $$r$$ user defined but constant. Varying $$W$$ will increase the number of agents within the environment, and hence the problem scale.  |
| Neighbourhood scale/Communication scale | This benchmark will test the simulators ability to handle increasing communication between agents given a fixed problem size and fixed number of processors, by increasing the size of each agents neighbourhood. | $$k_{rep}=1\times10^{-5}$$, $$k_{att}=1\times10^{-5}$$, $$\rho=0.01$$, $$r$$ varying creating increased communication, $$W$$ user defined but constant. |
| Entropy/Agent Activity | This benchmark will test the simulators ability to handle increasing motion of agents given a fixed problem size and number of processors. Most implementations will have stable performance throughout this benchmark. | $$r=5.0$$, $$\rho$$ and $$W$$ can be user defined but constant. Varying $$k_{rep}$$ and $$k_{att}$$ such that the difference between the two parameters increases, will increase agent speeds. |
| Nodes Scaling Benchmark | Benchmark to test the increasing number of processors on a fixed model size. This benchmark tests the scalability of a particular simulator and is not suitable for evaluation between simulators.| $$k_{rep}=1.0$$, $$k_{att}=0.0$$, $$r=2.0$$, $$\rho$$ and $$W$$ can be user defined but must remain constant. |
{:.decoratedtable}


# Reference Implementation

3D reference implementations in: FLAME GPU, MASON and Repast Simphony are available [here](https://github.com/Robadob/circles-benchmark), alongside a spreadsheet of results.

A 2D reference implementation is provided as part of the [FLAME GPU SDK](https://github.com/FLAMEGPU/FLAMEGPU/tree/master/examples/CirclesBruteForce_float/src/model).

