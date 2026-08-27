# Trisolaris: Numerical Simulation of Binary and Three-Body Stellar Systems

## Overview

This project develops a numerical framework in Python for modelling gravitationally interacting stellar systems in two-dimensional space.

The aim was to investigate how numerical methods can be used to solve coupled gravitational equations of motion and to explore the transition between stable periodic motion and chaotic dynamics.

The project progresses through three main stages:

1. **Binary Star System**  
   A stable two-body gravitational system is simulated and solved using both SciPy's adaptive `solve_ivp` solver and a manually implemented second-order midpoint method.

2. **Three-Body System**  
   The model is extended to three equal-mass stars. A stable figure-eight orbit is reproduced before the initial conditions are perturbed to generate chaotic behaviour and stellar ejection.

3. **Hypergiant Merger System**  
   The point-particle assumption is removed and finite-sized hypergiants are introduced. Stars merge when they pass within a specified distance, producing a dynamically changing system.

The project combines numerical methods, computational physics, object-oriented programming, convergence testing and scientific visualisation.

---

## Key Skills

### Numerical Methods

- Numerical solution of coupled ordinary differential equations
- Second-order midpoint integration
- Fourth-order Runge-Kutta integration
- Adaptive RK45 integration
- Numerical convergence testing
- Error analysis
- Phase-space analysis
- Dynamical systems modelling
- Sensitivity to initial conditions
- N-body gravitational simulation

### Programming

- Python
- Object-oriented programming
- Class inheritance
- Operator overloading
- NumPy array manipulation
- Defensive programming
- Input validation
- Scientific visualisation
- Modular simulation design

---

## Technologies

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| NumPy | Vector calculations and numerical state representation |
| SciPy | Adaptive ODE integration using `solve_ivp` |
| Matplotlib | Trajectory, phase-space and convergence plots |
| Jupyter Notebook | Development, testing and documentation |

---

# Physical Model

Each star is characterised by:

- mass
- two-dimensional position `(x, y)`
- two-dimensional velocity `(vx, vy)`

The acceleration of each star is determined by the gravitational attraction produced by every other star in the system.

The gravitational constant is set to:

```python
G = 1
```

This allows the simulation to operate using convenient dimensionless units.

The gravitational equations are second-order ordinary differential equations because acceleration depends on the second derivative of position.

To solve them numerically, I converted the equations into a first-order system by treating position and velocity as separate state variables.

For a binary system, the state vector is:

```text
[x1, y1, x2, y2, vx1, vy1, vx2, vy2]
```

The numerical integrator then evolves this complete state through time.

---

# Object-Oriented Structure

The project uses classes to separate the properties of individual stars from the numerical behaviour of the complete system.

## `Stars`

The `Stars` class stores:

```text
mass
position
velocity
```

Input validation was included to ensure that:

- mass is positive;
- position contains exactly two components;
- velocity contains exactly two components.

Invalid inputs raise errors rather than allowing the simulation to continue with physically invalid states.

---

## `StarSystem`

The binary `StarSystem` class manages:

- gravitational interactions;
- calculation of derivatives;
- SciPy integration;
- midpoint integration;
- phase-space analysis.

The system also checks that its initial total momentum is approximately zero.

---

## `ThreeBodySystem`

The three-body implementation extends the same idea to three stars.

The class:

- extracts positions and velocities from the state vector;
- calculates pairwise gravitational forces;
- sums acceleration contributions;
- integrates the equations using different numerical methods;
- produces trajectory plots;
- produces phase-space plots.

---

# Results

# 1. Binary Star System

## Objective

The first task was to simulate two stars undergoing stable gravitational orbits.

The initial conditions were:

### Star 1

```text
Mass     = 2.0
Position = [2.0, 0.0]
Velocity = [0.0, -0.1]
```

### Star 2

```text
Mass     = 1.0
Position = [-2.0, 0.0]
```

The velocity of Star 2 was not chosen arbitrarily.

It was calculated using conservation of momentum so that the total initial momentum of the system was zero.

This produced:

```text
Velocity = [0.0, 0.2]
```

The code also checks numerically that:

```text
total momentum ≈ 0
```

before beginning the simulation.

---

## Calculating the Gravitational Interaction

At each integration step, the displacement vector between the two stars is calculated.

The magnitude of this vector gives their separation.

The gravitational acceleration of each star is then calculated from the mass and position of the other star.

A defensive check is included for:

```python
r == 0
```

because overlapping point particles would otherwise produce a division-by-zero error in the gravitational force calculation.

---

# 2. Binary System Using SciPy `solve_ivp`

The first numerical integration used:

```python
scipy.integrate.solve_ivp
```

with the RK45 method.

RK45 is an adaptive Runge-Kutta solver, meaning that the timestep changes automatically depending on the estimated numerical error.

The system was integrated over multiple orbital periods.

## Result

The trajectory plot showed two closed gravitational orbits.

The stars remained gravitationally bound rather than escaping from the system or collapsing together.

The simulation also reproduced the expected qualitative behaviour:

- the stars move faster when they are closer together;
- the stars move more slowly when they are farther apart.

This occurs because gravitational attraction becomes stronger at smaller separations.

The closed trajectories indicate stable periodic motion.

---

# 3. Binary System Using the Midpoint Method

I then implemented the second-order midpoint method manually rather than relying entirely on SciPy.

For every timestep:

1. Calculate the derivative at the current state.
2. Use this derivative to estimate the state halfway through the timestep.
3. Calculate the derivative again at the midpoint.
4. Use the midpoint derivative to update the full state.

The simulation used:

```text
dt = 0.01
```

## Result

The midpoint method produced the same overall orbital structure as `solve_ivp`.

Both methods therefore captured the underlying gravitational dynamics.

However, the midpoint method uses a fixed timestep and produced a less smooth numerical solution.

Over longer simulations, numerical errors accumulated more noticeably than with adaptive RK45.

This demonstrated an important numerical trade-off:

```text
Midpoint method
→ simpler
→ fixed timestep
→ second-order accuracy

RK45
→ more complex
→ adaptive timestep
→ greater accuracy for demanding integrations
```

---

# 4. Binary Phase-Space Analysis

A trajectory plot describes where a star travels through physical space.

A phase-space plot instead examines its dynamical state by plotting position against velocity.

For Star 1, I plotted:

```text
y-position vs y-velocity
```

## Result

The phase-space trajectory formed a smooth closed loop.

A closed phase-space curve indicates that the star repeatedly returns to the same combinations of position and velocity.

This provides evidence that the binary system is periodic and stable.

The phase plot therefore provided an additional diagnostic beyond simply looking at the orbital trajectory.

---

# 5. Midpoint Convergence Test

Producing a physically reasonable graph does not automatically prove that a numerical method has been implemented correctly.

I therefore attempted to verify the expected convergence behaviour of the midpoint method.

The midpoint method is second order.

Therefore, when the timestep is halved, the leading numerical error should decrease by approximately:

```text
2² = 4
```

Three numerical resolutions were tested:

```text
N = 16
N = 32
N = 64
```

The procedure was:

1. Solve the same physical system at three resolutions.
2. Compare the medium-resolution solution with the coarse solution.
3. Compare the high-resolution solution with the medium solution.
4. Examine whether the difference decreases as the timestep becomes smaller.
5. Compare the reduction against the expected factor of four.

## Result

The differences between numerical solutions became smaller as the resolution increased.

This was consistent with numerical convergence.

However, the convergence plot did not reproduce the expected factor-of-four scaling as clearly as intended.

I therefore did not treat this test as a perfect verification of second-order convergence.

This was an important lesson from the project: numerical results need quantitative validation, and unexpected convergence behaviour should be investigated rather than ignored.

---

# 6. Stable Three-Body System

The binary model was next extended to three equal-mass stars.

The objective was to reproduce the stable figure-eight solution of the three-body problem.

The initial conditions used were:

### Star 1

```text
Mass     = 1.0
Position = [0.97000436, -0.24308753]
Velocity = [0.466203685, 0.43236573]
```

### Star 2

```text
Mass     = 1.0
Position = [-0.97000436, 0.24308753]
Velocity = [0.466203685, 0.43236573]
```

### Star 3

```text
Mass     = 1.0
Position = [0.0, 0.0]
Velocity = [-0.93240737, -0.86473146]
```

The system was simulated over:

```text
t = 0 → 20
```

---

# 7. Figure-Eight Orbit Using RK45

The three-body equations were first integrated using SciPy's RK45 solver.

At every timestep, the gravitational influence of the other two stars was calculated for each object.

## Result

The three stars followed the characteristic closed figure-eight trajectory.

The orbit remained:

- symmetrical;
- repeating;
- bounded;
- periodic.

No star escaped from the system.

The result demonstrated that a carefully selected set of initial conditions can produce a stable periodic solution even within the normally complex three-body problem.

---

# 8. Figure-Eight Orbit Using the Midpoint Method

The same initial conditions were then simulated independently using the manually implemented midpoint integrator.

The timestep used was:

```text
dt = 0.01
```

## Result

The midpoint solver also reproduced the figure-eight trajectory.

The similarity between the midpoint and RK45 results increased confidence that the gravitational equations were being implemented correctly.

However, the fixed-step midpoint method can accumulate more numerical error over long simulation periods.

---

# 9. Three-Body Phase-Space Analysis

A phase-space plot was generated for Star 3.

The variables plotted were:

```text
x-position vs x-velocity
```

## Result

The plot formed a closed and approximately symmetric trajectory.

This indicated that the star returned repeatedly to the same combinations of position and velocity.

The phase-space result was therefore consistent with the periodic figure-eight trajectory observed in physical space.

---

# 10. Fourth-Order Runge-Kutta Method

I also implemented a fourth-order Runge-Kutta integrator manually.

RK4 estimates the derivative four times during every timestep:

```text
k1
k2
k3
k4
```

These estimates are combined to calculate the next numerical state.

RK4 provides fourth-order accuracy and therefore offers a more accurate fixed-step method than the second-order midpoint scheme.

---

# 11. RK4 Convergence Test

A separate convergence test was performed for the RK4 implementation.

Because RK4 is fourth order, halving the timestep should reduce the leading numerical error by approximately:

```text
2⁴ = 16
```

The figure-eight system was simulated using:

```text
N = 16
N = 32
N = 64
```

The numerical differences between successive resolutions were then compared.

## Result

The error behaviour was interpreted as being consistent with the expected factor-of-16 reduction.

This provided evidence that the custom RK4 implementation was operating within the expected fourth-order convergence regime.

---

# 12. Creating a Chaotic Three-Body System

After reproducing the stable figure-eight solution, I investigated sensitivity to initial conditions.

The original velocities were deliberately perturbed.

One of the configurations used was:

```text
Star 1 velocity = [0.55, 0.43236573]

Star 2 velocity = [0.5, 0.5]

Star 3 velocity = [-1.5, -1.5]
```

The gravitational equations themselves were unchanged.

Only the initial conditions were altered.

The system was then integrated over:

```text
t = 0 → 20
```

using RK45.

---

# 13. Chaotic Three-Body Result

The behaviour changed completely.

The figure-eight symmetry disappeared and the trajectories became irregular.

The stars:

- crossed paths repeatedly;
- followed non-periodic trajectories;
- no longer maintained the original symmetry;
- experienced strongly varying gravitational interactions.

Eventually, Star 3 was ejected from the system.

Its trajectory extended away from the other two stars while the remaining stars continued interacting as a two-body system.

## Result

The simulation provided a computational demonstration of sensitivity to initial conditions.

The physical laws remained deterministic, but changing the starting velocities produced a dramatically different long-term outcome.

This is a defining feature of chaotic dynamical systems.

---

# 14. Stable vs Chaotic Phase Space

The effect of chaos was also visible in phase space.

## Stable system

The stable system produced a structured closed phase-space curve.

```text
Closed trajectory
→ repeating state
→ periodic behaviour
```

## Chaotic system

The perturbed system produced irregular loops and turns that did not repeat.

```text
Irregular trajectory
→ non-repeating state
→ chaotic behaviour
```

This showed that phase-space plots can provide a useful diagnostic for distinguishing periodic and chaotic dynamics.

---

# 15. Triple Hypergiant System

The final part of the project extended the simulation beyond point-like stars.

Three finite-sized hypergiants were introduced.

The initial mass ratio was:

```text
0.8 : 1.0 : 1.2
```

In the implementation these were scaled to:

```text
8 : 10 : 12
```

The initial positions were:

```text
[-1, 0]

[1, 0]

[0, √3]
```

The initial velocities were:

```text
[0.5, 0.3]

[-0.4, 0.6]

[-0.1, -0.8]
```

The radii were:

```text
0.5
0.6
0.7
```

The simulation parameters were:

```text
Merge distance = 1.0
dt             = 0.1
Time range     = 0 → 10
```

---

# 16. Hypergiant Class

A new class was created using inheritance:

```python
class Hypergiant(Stars):
```

This allowed the hypergiants to retain the existing:

- mass;
- position;
- velocity;

properties while adding:

```text
radius
```

A positive-radius validation check was also implemented.

---

# 17. Hypergiant Merger Logic

The `__add__()` operator was overloaded to combine two hypergiants.

When two hypergiants merge, the implementation calculates a new object.

## Mass

```text
new mass = mass1 + mass2
```

## Position

In my implementation, the new position is calculated using a mass-weighted combination:

```text
new position =
(mass1 × position1 + mass2 × position2)
/
(mass1 + mass2)
```

## Velocity

The new velocity is calculated using conservation of linear momentum:

```text
new velocity =
(mass1 × velocity1 + mass2 × velocity2)
/
(mass1 + mass2)
```

## Radius

For this simplified model:

```text
new radius = radius1 + radius2
```

The new properties are then used to create another `Hypergiant` object.

---

# 18. Detecting Mergers

The `StarSystem` calculates the separation between every pair of hypergiants.

A merger occurs when:

```python
distance < merge_distance
```

When this condition is satisfied:

1. the two stars are combined;
2. the merged star is removed from the original list;
3. a new combined hypergiant replaces the pair;
4. the number of stars in the simulation is updated;
5. numerical integration continues with the new system.

This differs from the earlier simulations because the number of objects is now allowed to change while the simulation is running.

---

# 19. Hypergiant Merger Result

The simulation began with:

```text
3 separate hypergiants
```

Gravitational attraction caused two of the objects to approach closely enough to satisfy the merger condition.

The system therefore transitioned from:

```text
3 stars → 2 stars
```

The resulting larger hypergiant continued interacting gravitationally with the remaining object.

A second merger then occurred:

```text
2 stars → 1 star
```

## Final Result

All three initial hypergiants eventually combined into one final massive hypergiant.

The trajectory plot shows the paths followed by the objects before merging and the subsequent motion of the final combined object.

---

# What I Learned

## 1. Numerical Results Need Verification

One of the main lessons from this project was that producing a realistic-looking simulation is not sufficient.

A numerical method should also be tested using:

- convergence studies;
- resolution comparisons;
- error analysis;
- independent numerical methods;
- physical conservation laws.

The imperfect midpoint convergence test was particularly useful because it demonstrated the importance of questioning numerical output instead of simply accepting it.

---

## 2. Solver Choice Matters

Different numerical integrators have different advantages.

### Midpoint

```text
Second-order
Fixed timestep
Simple to implement
Useful for understanding numerical integration
```

### RK4

```text
Fourth-order
Fixed timestep
More accurate than midpoint for comparable timestep sizes
```

### RK45 / solve_ivp

```text
Adaptive timestep
Automatic error control
Useful for complex systems where the relevant timescale changes
```

The best numerical method depends on the accuracy, performance and control required by the problem.

---

## 3. Initial Conditions Can Control Long-Term Behaviour

The three-body simulations demonstrated that a deterministic mathematical system does not necessarily produce easily predictable long-term behaviour.

The stable and chaotic simulations used the same gravitational equations.

Changing only the starting velocities transformed:

```text
stable periodic orbit
```

into:

```text
chaotic motion and stellar ejection
```

This developed my understanding of sensitivity to initial conditions and nonlinear dynamical systems.

---

## 4. Phase Space Reveals More Than Position Alone

Trajectory plots show where an object moves.

Phase-space plots show how its dynamical state evolves.

In this project:

```text
closed phase-space loops
```

were associated with periodic motion, while:

```text
irregular non-repeating phase trajectories
```

were associated with chaotic behaviour.

---

## 5. Physics Can Be Used to Validate Code

Physical laws can also act as numerical checks.

Examples used in this project include:

- zero initial net momentum;
- momentum conservation during mergers;
- positive masses;
- valid two-dimensional position vectors;
- valid two-dimensional velocity vectors.

This showed me how physical constraints and software validation can be combined.

---

## 6. Object-Oriented Programming Is Useful for Physical Modelling

Classes provided a natural way to represent physical objects.

The project separated:

```text
Star properties
↓
System interactions
↓
Numerical integration
↓
Physical events
```

Inheritance then allowed the original `Stars` model to be extended into a `Hypergiant` model without rewriting the entire simulation.

---

# Future Applications

The methods developed in this project can be applied to significantly more complex numerical problems.

## Larger N-Body Systems

The gravitational interaction model could be extended to simulate larger systems such as:

- planetary systems;
- star clusters;
- asteroid populations;
- satellite systems;
- simplified galactic models.

---

## Quantitative Chaos Analysis

The chaotic section could be extended beyond visual trajectory comparison.

Possible future techniques include:

- Lyapunov exponents;
- systematic perturbation experiments;
- Poincaré sections;
- orbital stability maps;
- nearby-trajectory separation analysis.

These would allow chaos to be measured quantitatively rather than identified mainly from trajectories.

---

## Conservation-Law Monitoring

Future versions could track:

```text
Total energy
Total linear momentum
Total angular momentum
Centre-of-mass position
```

throughout the simulation.

This would provide stronger validation of the numerical methods and help distinguish genuine physical effects from accumulated numerical error.

---

## Symplectic Integration

For long-term orbital simulations, symplectic numerical integrators could be investigated.

These methods are designed for Hamiltonian systems and can preserve important geometric and conservation properties better than conventional integrators over very long simulation times.

---

## Adaptive Error Control

The manual midpoint and RK4 integrators could be extended with adaptive timestep control.

The timestep could automatically decrease when stars experience close encounters and increase when the system evolves more slowly.

This would improve both computational efficiency and accuracy.

---

## Event-Driven Simulations

The hypergiant merger model introduced events that dynamically change the structure of the simulation.

The same approach could be applied to:

- particle collisions;
- fragmentation;
- chemical reaction models;
- threshold-triggered processes;
- agent-based simulations;
- population models.

---

## General Scientific Computing

The broader workflow developed in this project:

```text
Physical model
↓
Mathematical equations
↓
Numerical algorithm
↓
Python implementation
↓
Verification
↓
Visualisation
↓
Physical interpretation
```

is applicable to a wide range of computational science, engineering, quantitative modelling and data-driven research problems.

---

# Repository Structure

```text
trisolaris-three-body-simulation/
│
├── README.md
│
├── Coursework1_Trisolaris2024.ipynb
│
├── figures/
│   ├── binary_rk45.png
│   ├── binary_midpoint.png
│   ├── binary_phase_space.png
│   ├── midpoint_convergence.png
│   ├── figure_eight_rk45.png
│   ├── figure_eight_midpoint.png
│   ├── three_body_phase_space.png
│   ├── rk4_convergence.png
│   ├── chaotic_three_body.png
│   ├── chaotic_phase_space.png
│   └── hypergiant_merger.png
│
└── requirements.txt
```

---

# Running the Project

Clone the repository:

```bash
git clone <repository-url>
cd trisolaris-three-body-simulation
```

Install the required packages:

```bash
pip install numpy scipy matplotlib jupyter
```

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Then open:

```text
Coursework1_Trisolaris2024.ipynb
```

and run the notebook cells in order.

---

# Requirements

The main Python dependencies are:

```text
numpy
scipy
matplotlib
jupyter
```

These can be stored in a `requirements.txt` file as:

```text
numpy
scipy
matplotlib
jupyter
```

---

# Project Summary

This project demonstrates the complete process of translating a physical problem into a numerical simulation.

Starting from Newtonian gravity, I built numerical models capable of reproducing:

- stable binary orbits;
- the three-body figure-eight solution;
- periodic phase-space behaviour;
- numerical convergence;
- chaotic three-body dynamics;
- stellar ejection;
- dynamically triggered hypergiant mergers.

The project strengthened my understanding of numerical ODE solving, computational physics, convergence testing, dynamical systems, object-oriented programming and scientific Python.
