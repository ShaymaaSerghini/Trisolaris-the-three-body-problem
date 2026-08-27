## Trisolaris: Numerical Simulation of Stellar Systems

### Overview

This project uses Python to simulate gravitationally interacting stars and explore **stable, periodic and chaotic dynamical systems**.

I developed simulations for:

- Stable binary star orbits
- The three-body figure-eight orbit
- Chaotic three-body dynamics and stellar ejection
- Hypergiant collisions and mergers

The equations of motion were solved using a custom **Midpoint method**, custom **RK4 integrator**, and SciPy's adaptive **RK45 `solve_ivp`** solver.


### Technologies & Skills

**Python · NumPy · SciPy · Matplotlib · Jupyter Notebook**

Key skills:

- Numerical ODE solving
- Runge-Kutta methods
- Convergence testing
- N-body simulation
- Object-oriented programming
- Phase-space analysis
- Chaotic dynamical systems
- Scientific visualisation
- Defensive programming
- Conservation laws


## Results

### 1. Binary Star System

Two stars were modelled using Newtonian gravity.

Initial conditions were chosen so that the total momentum of the system was zero:

```text
Star 1: mass = 2, position = [2,0], velocity = [0,-0.1]
Star 2: mass = 1, position = [-2,0], velocity = [0,0.2]
```

The system was solved using both **RK45** and my own **second-order Midpoint integrator**.

#### Result
<img width="543" height="424" alt="image" src="https://github.com/user-attachments/assets/35a12998-d455-4015-9cdf-ab729ed75dad" />

<img width="540" height="342" alt="image" src="https://github.com/user-attachments/assets/8aca366f-3132-462e-963a-6e0b80a582c3" />

Both methods produced stable closed orbits.

RK45 produced a more accurate solution over long integrations because it automatically adapts its timestep.


### 2. Phase-Space Analysis

For one star, I plotted:

```text
position vs velocity
```

#### Result
<img width="544" height="349" alt="image" src="https://github.com/user-attachments/assets/4d6af333-cd69-4094-9880-b98735f4fb79" />

The phase-space trajectory formed a closed loop, confirming periodic motion and orbital stability.


### 3. Midpoint Convergence Test

The Midpoint method should show second-order convergence.

I tested:

```text
N = 16, 32, 64
```

Halving the timestep should reduce the error by approximately:

```text
2² = 4
```

#### Result

<img width="541" height="402" alt="image" src="https://github.com/user-attachments/assets/f2f705e8-9fc5-443c-a26e-0cc2355eb33a" />

The numerical error decreased with increasing resolution, although the expected factor-of-four scaling was not reproduced as clearly as intended.

This highlighted the importance of verifying numerical simulations rather than relying only on visually reasonable results.


### 4. Stable Three-Body System

The model was extended to three equal-mass stars using initial conditions for the known **figure-eight orbit**.

The system was independently solved using:

- RK45
- Midpoint
- RK4

#### Result
<img width="545" height="342" alt="image" src="https://github.com/user-attachments/assets/f5163528-ad3a-4480-9ff6-972e06e5f4ef" />

<img width="542" height="343" alt="image" src="https://github.com/user-attachments/assets/17a404b5-070c-46ca-8c6d-09f03b61fb87" />

The stars followed a stable, symmetric and repeating figure-eight trajectory.

Both RK45 and Midpoint reproduced the same overall behaviour.


### 5. RK4 Convergence

I implemented a fourth-order Runge-Kutta solver and tested it using:

```text
N = 16, 32, 64
```

For RK4, halving the timestep should reduce numerical error by approximately:

```text
2⁴ = 16
```

#### Result

The convergence behaviour was consistent with the expected fourth-order scaling.
<img width="542" height="423" alt="image" src="https://github.com/user-attachments/assets/54a08916-c3ad-4d40-ba4c-c1a1f98455f3" />


### 6. Chaotic Three-Body System

The stable figure-eight initial velocities were deliberately perturbed.

The gravitational equations remained unchanged.

#### Result

<img width="540" height="539" alt="image" src="https://github.com/user-attachments/assets/9249216a-83c3-4140-aafe-76239840e7ae" />


The regular figure-eight orbit disappeared and the trajectories became irregular and non-periodic.

Eventually, one star was **ejected from the system**, demonstrating sensitivity to initial conditions.

The stable system produced closed phase-space trajectories, while the chaotic system produced irregular, non-repeating phase-space behaviour.



### 7. Hypergiant Merger Simulation

The model was extended to finite-sized stars capable of merging.

A `Hypergiant` class inherited from the original `Stars` class and added:

```text
radius
merger behaviour
```

A merger occurs when:

```python
distance < merge_distance
```

When two stars merge:

- Masses are added
- Position is recalculated
- Velocity is determined using conservation of momentum
- Radius is updated

#### Result

The system evolved from:

```text
3 hypergiants
      ↓
2 hypergiants
      ↓
1 final merged hypergiant
```

This required the simulation to dynamically change the number of objects while running.
<img width="543" height="348" alt="image" src="https://github.com/user-attachments/assets/9697bc91-3178-40fe-b503-71caa13a4cba" />

<img width="542" height="329" alt="image" src="https://github.com/user-attachments/assets/c82ba516-6039-4060-911a-6f7539a7606a" />

## What I Learned

Through this coursework, I learned how to translate mathematical models of physical systems into working numerical simulations in Python. I applied Newtonian gravitational equations to binary and three-body systems, converted the governing equations into a form suitable for numerical integration, and used different numerical methods to investigate how the systems evolved over time.

I also learned the importance of **numerical validation**. A simulation can look physically reasonable while still containing significant numerical error, so I used convergence testing, comparisons between independent solvers, and physical quantities such as momentum and orbital behaviour to assess whether my results were reliable. Implementing the Midpoint and RK4 methods myself helped me understand how numerical accuracy depends on timestep size and the order of the integration method.

This project also showed me how the choice of numerical solver affects a scientific computation. By comparing my own fixed-step Midpoint and RK4 implementations with SciPy's adaptive RK45 solver, I developed a better understanding of the trade-offs between computational simplicity, accuracy, stability and adaptive error control.

Studying the three-body problem gave me practical experience with **nonlinear and chaotic dynamical systems**. I saw directly how a small perturbation to the initial conditions could completely change the long-term evolution of the system, transforming a stable periodic figure-eight orbit into irregular motion and eventually causing one star to be ejected.

I also developed my **object-oriented programming** skills. I used classes to represent stars and extended the original `Stars` class through inheritance to create a `Hypergiant` class with additional physical properties and merger behaviour. This taught me how scientific code can be structured so that models can be extended without rewriting the entire simulation.

Throughout the coursework, I used **Git and GitHub** to manage and document the development of the project. This gave me experience maintaining a structured scientific coding project, tracking changes to my code and presenting numerical results clearly alongside the implementation.

Overall, this project showed me how concepts from physics, mathematics and programming can be combined to investigate complex systems computationally rather than treating them as separate areas of study.

## Applying What I Learned

The numerical and programming techniques I developed in this project are transferable to many other computational problems. In future projects, I would reuse the same approach of first defining the mathematical model, translating it into numerical equations, validating the implementation and then analysing the resulting behaviour.

The numerical integration methods could be applied to problems involving:

* larger N-body gravitational systems;
* planetary and satellite dynamics;
* stellar clusters;
* collision and particle simulations;
* other systems governed by coupled differential equations.

I would also reuse the validation techniques I developed, particularly **convergence testing, comparison between independent numerical methods and checking physical constraints**, because they provide a way of determining whether a computational result can actually be trusted.

The object-oriented structure could also be extended to more complicated simulations by introducing additional particle types, physical interactions or collision rules. More generally, the project gave me a framework I can reuse in future computational physics and quantitative modelling work: **build the model, implement it carefully, test its numerical behaviour, validate the result and only then interpret the output.**
