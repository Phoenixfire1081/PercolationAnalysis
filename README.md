# Identifying non-subjective thresholds for scalar indicators of coherent structures with percolation analysis

This page has been setup to support the preprint titled **_Geometry and organization of coherent structures in stably stratified atmospheric boundary layers_** which can be found in arXiv: https://arxiv.org/abs/2110.02253

This idea originated in the work of Moisy & Jiménez 2004, JFM [1] while inspecting vorticity structures in isotropic turbulence and later adopted by Del Alamo & Jiménez 2006, JFM [2] for wall-bounded flows. It makes use of the observation that the ratio of the volume of the largest structure in the domain ($V_{max}$) over the volume of all structures ($V$) undergoes a percolation transition for increasing threshold values ($\tau$). At the lowest $\tau$, a seemingly infinite cluster appears as $V_{max} = V$ (a large structure spans the entire domain) and as $\tau$ is increased, the cluster breaks down into simpler structures. The percolation threshold is defined as the threshold at which the slope of $V_{max}/V$ is maximum.

For wall-bounded flows, a necessary condition is that the scalar indicator has to be normalized with its root-mean-square over wall-parallel planes to ensure that a single, global threshold can be chosen to highlight the structures uniformly (see eq 3.1 of [2]). This was further explored for other scalar indicators of coherent structures such as low-and high-speed streaks, sweeps, ejections, shear layers, backs, and bulges in the work of Harikrishnan et al., 2021 [3].

If you use this code for your work, please cite our arXiv preprint [4]:
```
@article{harikrishnan2021geometry,
  title={Geometry and organization of coherent structures in stably stratified atmospheric boundary layers},
  author={Harikrishnan, Abhishek and Ansorge, Cedrick and Klein, Rupert and Vercauteren, Nikki},
  journal={arXiv preprint arXiv:2110.02253},
  year={2021}
}
```

## Installation

The code is available as a package from PyPI: https://pypi.org/project/percolationAnalysis/ and acts as a wrapper to the extraction script: https://github.com/Phoenixfire1081/CoherentStructureExtraction. First, install the latter script,

```
pip install extractstructuresMC
```
followed by,
```
pip install percolationAnalysis
```

## Example - Calculating the percolation threshold from the given test data

Download testData.bin from the GitHub website first.

```
import numpy as np
import array
from percolationAnalysis import percolationAnalysis

# Select file to run tests on
_filenameRead = 'testData.bin'

# What indexing does it use?
# This refers to array order
# See here for a full description: https://docs.oracle.com/cd/E19957-01/805-4940/z400091044d0/index.html
# Python uses C-order indexing (or row-major order). For this set _zFastest = True
_zFastest = False

# Set data related parameters
xlen = 100 
ylen = 50
zlen = 200

# Set precision of binary data. 'f' is 32-bit floating point data.
# For others, see here: https://docs.python.org/3/library/array.html
precision = 'f'

# Use array to read data. This is fast for binary files.
data = array.array(precision)
fr = open(_filenameRead, 'rb')
data.fromfile(fr, (xlen*ylen*zlen))
fr.close()

# Convert to numpy array
data = np.array(data, dtype = np.float32)

# Threshold values for percolation analysis
# Split evenly and take the first 50 values or so
# The transition can be usually seen within this range
# To see a sharper transition, reduce the number of evenly spaced numbers
_threshVals = np.linspace(0, data.max(), 500)[:49]

# Print out details
_verbose = True

# Use Marching Cubes neighbor correction (more computation power required)
_marchingCubesExt = True

percolationObj = percolationAnalysis(_threshVals, data, xlen, ylen, zlen, \
_zFastest, _verbose, _marchingCubesExt)
percolationObj.processThresholds()

# Plot the data and identify the percolation threshold
filename = 'Percolation_threshold.txt'
_min, _max, _mean = percolationObj.plotPercolation(filename)
```
Running the above script on testData.bin produces the following loglog plot:

![Screenshot](Percolation.png)

The region of percolation transition is clearly visible. The plot highlights the thresholds corresponding to the maximum slope of $V_{max}/V$. Normally, the mean value is used. However, the minimum and maximum are also acceptable choices of threshold. 

## References

[1] Moisy, Frédéric, and Javier Jiménez. "Geometry and clustering of intense structures in isotropic turbulence." Journal of fluid mechanics 513 (2004): 111-133.

[2] Del Alamo, Juan C., et al. "Self-similar vortex clusters in the turbulent logarithmic region." Journal of Fluid Mechanics 561 (2006): 329-358.

[3] Harikrishnan, Abhishek, et al. "Geometry and organization of coherent structures in stably stratified atmospheric boundary layers." arXiv preprint arXiv:2110.02253 (2021).
