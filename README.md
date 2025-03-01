# mcmc-estimator
A Markov Chain Monte Carlo (MCMC) process that estimates a function's parameters using Metropolis–Hastings to fit data. See the notebook for all examples (damped oscillation example shown below).

### Notebook Example: Damped Oscillation
$$y(x)=A⋅e^{−kx}⋅cos(ωx+ϕ)$$

```
import numpy as np
from mcmc_estimator import MCMCParameterEstimator as mcmc

np.random.seed(42)

# Test data
A_true = 3.0      # Amplitude
k_true = 0.5      # Decay rate
omega_true = 2.0  # Angular frequency
phi_true = 0.5    # Phase offset
sigma_true = 0.3  # Noise level
np.random.seed(42)
x_data = np.linspace(0, 10, 100)  # x spans 0 to 10
y_true = A_true * np.exp(-k_true * x_data) * np.cos(omega_true * x_data + phi_true)
y_data = y_true + np.random.normal(0, sigma_true, size=len(x_data))

# Define model function
def damped_oscillation(params, x):
    A, k, omega, phi = params
    return A * np.exp(-k * x) * np.cos(omega * x + phi)

# MCMC Estimator
estimator = mcmc(
    model_fn=damped_oscillation,
    x_data=x_data,
    y_data=y_data,
    initial_params=[1.0, 1.0, 1.0, 0.0],  # Initial guess for A, k, omega, phi
    step_size=[0.2, 0.05, 0.1, 0.1],      # Step sizes tuned for different scales
    n_iterations=30000,
    burn_in=0.3,
    sigma_obs=sigma_true,
    random_seed=42
)
estimator.run()
estimator.summary()
```
Acceptance Rate = 0.113  
Parameter Estimates:  
Param 0: mean=3.157, std=0.211, 95% CI=(2.770, 3.606)  
Param 1: mean=0.491, std=0.042, 95% CI=(0.417, 0.580)  
Param 2: mean=2.002, std=0.044, 95% CI=(1.914, 2.087)  
Param 3: mean=0.443, std=0.060, 95% CI=(0.323, 0.560)  
```
estimator.plot_diagnostics()
```

![mcmc-estimator](https://github.com/nickgerend/mcmc-estimator/raw/main/assets/est_3_trace.png)
```
estimator.plot_fit()
```
![mcmc-estimator](https://github.com/nickgerend/mcmc-estimator/raw/main/assets/est_3_fit.png)
```
# Access the parameter samples and calculate the means
samples = estimator.get_samples()
print(samples.mean(axis=0))
```
[3.15697316 0.49097106 2.00200042 0.44327931]