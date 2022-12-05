# Video Games

## Simulation

Approximation, simplification of reality

## Agent-based

Simulation in which distinct agents interact

## Temporal Simulation
Dynamic, state of the world changes over time

## Interactive Temporal Simulation
Needs to react to unpredictable input

## Interactive Real-Time Simulation
Needs to react in real-time

##  Soft Real-Time System

### Deadline
- Updating the screen 30-60 times per second
- Updating the physics 120 times per second
- Updating the AI once per second

### Soft Deadline
Missed Deadlines are not catastrophic

## Mathematical Models

### Analytical

Expressed as a Formula

```
y(t) = 12 gt2 + v0t + y0.
```

- Very convenient and performant
- Many problems don't have a closed-form solution

### Numerical

```
y(t + ∆t) = F(y(t), y ̇(t), y ̈(t), . . .).
```

Expressed as one step/iteration that follows on a previous step

# Game Engines

- Engines can be very specialized or very general purpose
- The more general-purpose, the less optimal
- Efficiency entails making trade-offs
  - Those are made based on assumptions about the usage / target hardware
  - e.g. Rendering Optimizations for Indoor-Environment (BSP) don't work well for Outdoor-Environment (LOD)

