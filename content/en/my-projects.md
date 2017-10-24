---
lang: en
slug: my-projects
title: "My Projects"
date: 2014-10-04
categories: earthengine
---

Below is an incomplete list of packages and projects I have worked on. For a full list, please see [my cv](/cv).

# Global Food Security Analysis-Support Data at 30 Meters

Aiming to mapping global croplands at 30 meter resolution, we made [products](https://croplands.org/) freely available for educational, research, or commercial applications. Four components are integrated to make this project possible:

1. a composition of global 30-meter multi-spectrum multi-season base dataset designed for cropland mapping; 
2. online crowdsourcing cropland data interpretation application (croplands.org);
3. testing-bed/data-flow for data mining over large scale with active users; 
4. hybrid method to incorporate classification models and segmentation algorithm.

# Auto-calibrated Surface Evapotranspiration (ET) Mapping Algorithm (ASEMA)

ASEMA is an variant of SEBAL which calculates the actual ET_a and potential evapotranspiration rates ET_p as well as other energy exchanges between land and atmosphere. The key input data for ASEMS consists of spectral radiance in the visible, near-infrared and thermal infrared part of the spectrum. ASEMA computes a complete radiation and energy balance along with the resistances for momentum, heat and water vapor transport for every individual pixel. The resistances are a function of state conditions such as soil water potential (and thus soil moisture), wind speed and air temperature and change from day-to-day. Satellite radiances will be converted first into land surface characteristics such as surface albedo, leaf area index, vegetation index and surface temperature. These land surface characteristics can be derived from different types of satellites. First, an instantaneous evapotranspiration is computed, that is subsequently scaled up to 24 hours and longer periods according to daily reference ETr.

# Earthengine Community

[Site-Entry](https://suredream.github.io/ee-community/)


