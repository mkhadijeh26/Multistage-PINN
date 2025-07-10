# To cite:
```Article
@article{khadijeh2025multistage,
  title={Multistage physics informed neural network for solving coupled multiphysics problems in material degradation and fluid dynamics},
  author={Khadijeh, Mahmoud and Cerqueglini, Veronica and Kasbergen, Cor and Erkens, Sandra and Varveri, Aikaterini},
  journal={Engineering with Computers},
  pages={1--31},
  year={2025},
  publisher={Springer},
  doi ={doi.org/10.1007/s00366-025-02174-4}
}
```






# Physics-Informed Neural Network (PINN) with Gaussian Process for Coupled PDE System

This repository contains a comprehensive implementation of a Physics-Informed Neural Network (PINN) enhanced with Gaussian Process (GP) regression for solving a coupled system of partial differential equations (PDEs) representing heat transfer, oxygen pressure dynamics, and carbonyl area evolution.

## Overview

The code implements a sophisticated approach to solve a multi-physics problem involving:
- **Heat Transfer**: Governed by the diffusion equation
- **Oxygen Pressure Dynamics**: Modeled with simplified pressure diffusion
- **Carbonyl Area Evolution**: Coupled chemical reaction kinetics

The solution methodology combines:
1. **Physics-Informed Neural Networks** for learning the underlying physics
2. **Curriculum Learning** for progressive training complexity
3. **Gaussian Process Regression** for residual correction and uncertainty quantification

## Problem Formulation

### Physical Domain
- **Spatial Domain**: 2D rectangular domain (1.0 × 1.0 m)
- **Time Domain**: 0 to 1.0 s
- **Variables**: Temperature (T), Oxygen Pressure (Pb), Carbonyl Area (CAb)

### Governing Equations

1. **Heat Transfer Equation**:
   ```
   ∂T/∂t = α(∂²T/∂x² + ∂²T/∂y²)
   ```
   where α = 0.1 is the thermal diffusivity

2. **Oxygen Pressure Equation**:
   ```
   ∂Pb/∂t = D(∂²Pb/∂x² + ∂²Pb/∂y²) + coupling_terms
   ```

3. **Carbonyl Area Equation**:
   ```
   ∂CAb/∂t = f(kf, kc, t)
   ```
   where kf and kc are temperature and pressure-dependent reaction rates

### Boundary and Initial Conditions

**Temperature**:
- Top/Bottom boundaries: 303.15 K
- Left/Right boundaries: 274.15 K
- Initial condition: 295.65 K

**Oxygen Pressure**:
- Top/Bottom boundaries: 1×10⁷ Pa
- Left/Right boundaries: 1×10⁶ Pa
- Initial condition: 1×10⁷ Pa

**Carbonyl Area**:
- Top/Bottom boundaries: 1.0
- Left/Right boundaries: 0.94
- Initial condition: 0.94

## Key Features

### 1. Curriculum Learning
The training process is divided into three progressive stages:
- **Stage 1**: Heat transfer only (single variable)
- **Stage 2**: Heat transfer + oxygen pressure (two variables)
- **Stage 3**: Full coupled system (three variables)

Each stage gradually increases domain complexity and variable coupling.

### 2. Variable Scaling
All variables are normalized to [0,1] range to improve training stability:
```python
scaled_var = (var - var_min) / (var_max - var_min)
```

### 3. Neural Network Architecture
- **Input**: 3D coordinates (x, y, t)
- **Hidden Layers**: 4 layers with 10 neurons each
- **Activation**: Tanh functions
- **Output**: 3 variables (T, Pb, CAb)

### 4. Gaussian Process Enhancement
After PINN training, a GP is trained on PDE residuals to:
- Correct systematic errors
- Provide uncertainty estimates
- Improve overall accuracy

## Implementation Details

### Loss Function Components
1. **PDE Residual Loss**: Enforces physics equations
2. **Boundary Condition Loss**: Ensures proper boundary values
3. **Initial Condition Loss**: Maintains initial state consistency

### Training Configuration
- **Optimizer**: Adam with learning rate 0.001
- **Epochs per stage**: 100,000
- **Collocation points**: 1,000 per stage
- **Plotting frequency**: Every 2,000 epochs

### Data Generation
- **Collocation Points**: Random sampling in space-time domain
- **Boundary Points**: Systematic sampling along domain edges
- **Initial Points**: Random spatial sampling at t=0

## Outputs and Visualization

### Generated Files
1. **Heatmaps**: Visual representation of field variables
   - `heatmap_stage_X_epoch_Y.png`: Training progress visualization
   - `heatmap_pinn_gp_stage_3.png`: Final PINN+GP results

2. **Loss Curves**: Training convergence analysis
   - `training_losses.png`: Component loss evolution

3. **Predictions**: Quantitative results
   - `predictions.xlsx`: Line predictions along domain centerlines

### Performance Metrics
The code computes and reports:
- Mean Squared Error (MSE) for each variable
- Comparison between PINN-only and PINN+GP