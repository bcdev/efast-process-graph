# EFAST OpenEO UDP

This repository contains an implementation of the *Efficient Fusion Algorithm Across Spatio-Temporal Scales* (EFAST) \[1\]
as a parametrized OpenEO process graph, which can be executed as a user defined process (UDP).

This implementation has been created in the context of the ESA funded [Application Propagation Environment (APEx) initiative](https://apex.esa.int).

The creators of the algorithm, DHI-GRAS, provide a [Python implementation](https://github.com/DHI-GRAS/efast), which served as a reference for the UDP.

\[1\]: Senty, Paul, Radoslaw Guzinski, Kenneth Grogan, et al. “Fast Fusion of Sentinel-2 and Sentinel-3 Time Series over Rangelands.” Remote Sensing 16, no. 11 (2024): 1833.

## EFAST

EFAST is a method to create time-series with a fine resolution in space and time from a fine spatial but coarse temporal resolution source
(Sentinel-2) and a coarse temporal but fine spatial resolution source (Sentinel-3).
It computes a user-defined time series, which can have fine temporal resolution, at fine spatial resolution (10m in the case of the UDP).

## Usage

```Python
import openeo

connection = openeo.connect("https://openeo.dataspace.copernicus.eu").authenticate_oidc()

cube = connection.datacube_from_process(
    # process id
    "efast",
    # where to find the process graph definition (this repo)
    namespace="https://raw.githubusercontent.com/bcdev/efast-process-graph/refs/heads/main/process_graph.json",
    # SENTINEL2_L2A bands (here, red and nir for NDVI computation)
    s2_data_bands=["B04","B8A"],
    # SENTINEL3_SYN_L2_SYN bands with matching center frequencies to `s2_bands`
    s3_data_bands=["SDR_Oa08", "SDR_Oa17"],
    # Temporal extent of the inputs
    temporal_extent=["2023-09-01", "2023-11-01"],
    # Temporal extent of the output (should be inside `temporal_extent`)
    temporal_extent_target=["2023-09-03", "2023-10-29"],
    # Spatial extent of inputs and outputs
    spatial_extent={
        "west": -15.9,
        "south": 15.67,
        "east": -15.3,
        "north": 16.27,
    },
    # Interval of the output time series
    interval_days=5,
    # Smoothing parameter controlling the amount of temporal context considered in fusion (standard deviation of gaussian window)
    temporal_score_stddev=20,
    # Output NDVI (if False, output a cube with bands `s2_data_bands`)
    output_ndvi=True,
```

For a detailed usage example and description of the parameters, see the [example notebook](notebooks/EFAST_example.ipynb).
