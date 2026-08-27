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

Both methods produced stable closed orbits.

RK45 produced a more accurate solution over long integrations because it automatically adapts its timestep.


### 2. Phase-Space Analysis

For one star, I plotted:

```text
position vs velocity
```

#### Result

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

The numerical error decreased with increasing resolution, although the expected factor-of-four scaling was not reproduced as clearly as intended.

This highlighted the importance of verifying numerical simulations rather than relying only on visually reasonable results.


### 4. Stable Three-Body System

The model was extended to three equal-mass stars using initial conditions for the known **figure-eight orbit**.

The system was independently solved using:

- RK45
- Midpoint
- RK4

#### Result

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


### 6. Chaotic Three-Body System

The stable figure-eight initial velocities were deliberately perturbed.

The gravitational equations remained unchanged.

#### Result

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


## What I Learned

#### Numerical validation matters

A realistic-looking simulation is not enough. Numerical results should be checked using convergence tests, independent solvers and physical constraints.

#### Solver choice matters

I developed an understanding of the trade-offs between:

- Midpoint — simple, second-order
- RK4 — fixed-step, fourth-order
- RK45 — adaptive timestep and error control

#### Nonlinear systems can be highly sensitive

Small changes to the initial conditions transformed a stable periodic system into chaotic motion and stellar ejection.

#### Object-oriented design improves scientific code

Classes allowed physical objects, numerical methods and interactions to be organised separately and extended through inheritance.


## Future Applications

The methods used here could be extended to:

- Larger N-body simulations
- Planetary and satellite systems
- Star clusters
- Collision and particle simulations


