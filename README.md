# File S1. Source code and sample network for reproducing the analysis.

## This archive contains Python and R notebooks implementing the simulation models and graph construction procedures used in the study. A sample network file is included to demonstrate execution. The notebooks can be run in standard environments such as Google Colab and include all necessary steps to obtain the presented results.

# Code and data

## Simulation notebooks accompanying **“Collective dynamics, information reinforcement and autonomy loss in human–AI networks.”**

## Files

- `Model_1.ipynb` — AIA-dominated model: information-field and marker-state dynamics; plots for Figs. 3–6.
- `Model_2.ipynb` — NIA-dominated model: preference-weighted network interactions, threshold calculations, and plots for Figs. 7–9.
- `Graph_parameters.ipynb` — network statistics, adjacency eigenvalues, topology visualization, and degree distribution for Fig. 2.
- `R_Graph.ipynb` — example of power-law degree-sequence sampling and graph construction with R/igraph. This example does not set a fixed random seed.
- `N40_y2.4.edgelist` — preserved 40-node network used in the simulations. Load this file to reproduce the calculations; regenerating the graph is unnecessary.

## Numerical implementation

The Python notebooks use NumPy, SciPy, NetworkX, and Matplotlib.

- `laser_ode` (Model 1) and `social_network_ode` (Model 2) calculate the time derivatives of the complex field and the marker states. In Model 2, the neighbor field uses the fixed target values `sigma0`, while the local multiplier `sigma` evolves in time.
- `solve_fields` and `solve_social_fields` combine the real and imaginary field components with the marker states into `3N` real variables. They integrate from `0` to `T` with `scipy.integrate.solve_ivp`, using adaptive `BDF`, `rtol=1e-6`, and `atol=1e-9`, and return time, complex fields, amplitudes, intensities, and marker states. Integration ends at the specified time; no stationarity-based stopping rule is used.
- `mean_curves` and `mean_social_curves` return degree-weighted averages of the intensities and marker states, shown as black dashed curves.

The initial field is real and uniform: `E_i(0) = 0.01`. Random marker-state values are generated with `numpy.random.default_rng` and clipped to the bounds specified in each figure block. Seeds are 25 for Fig. 3, 35 for Figs. 4–5 and 7–9, and 30 for Fig. 6. Parameters, initial conditions, and integration intervals are defined in the corresponding notebook cells.

The leading adjacency eigenvalue is obtained numerically from the adjacency spectrum. The Molloy–Reed ratio is calculated as `sum(k**2) / sum(k)`. For the preserved graph, these values are approximately `8.12054` and `9.86170`, respectively.

## Graph construction in R

In `R_Graph.ipynb`, `generate_power_law_degrees` samples degrees with weights proportional to `k^(-gamma)`. `generate_scale_free_graph` repeats this sampling until the degree sum is even, then constructs and exports the graph using:

```r
g <- igraph::sample_degseq(degrees, method = "vl")
write.table(igraph::as_edgelist(g), "N40_y2.4.edgelist",
            row.names = FALSE, col.names = FALSE)
```

The example uses `n = 40`, `gamma = 2.4`, and `k_min = 3`. It has no fixed seed and writes the same filename as the preserved simulation network; use the preserved file for reproducing the figures.

## Run
1. Open a Python notebook in Google Colab or Jupyter.
2. Place `N40_y2.4.edgelist` in its working directory, keeping this filename unchanged.
3. Run the cells in order, starting with imports and graph loading.

The R notebook requires an R kernel and `igraph`; it is not required to run the Python simulations.
