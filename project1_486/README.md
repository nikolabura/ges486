# Project 1 GES486 Nikola Bura

*March 23, 2021*

__Project focus:__ Exploring electricity generation and renewables utilization in the continental US.

This project is split into two parts:

1) **Wind turbine distribution and site suitability** in the continental US; exploring where turbines are currently built and which areas might be a good fit for future expansion and development.
2) **Energy mix over time**; exploring, through the use of animated maps, how the "energy mix" of states (proportion of renewable power, vs. coal, vs. gas) changes over the course of a year.

(I split the project into two parts because I felt that neither of these concepts by themselves required much work or yielded much useful data. If focusing on just one part is a requirement, then I think part 2, the energy mix, is more interesting.) Both parts were done in QGIS.

## 1 - Wind Turbine Distribution and Suitability

### Topic

The US is rapidly constructing wind turbines, and jobs in the wind power industry have been growing for a while now. Let us investigate current geographical patterns in wind turbine placement, and speculate on possible areas for future development/construction.

### Data

Existing wind turbine data is available from the USA Energy Information Administration at https://atlas.eia.gov/datasets/wind-2.
Wind speed and _wind power density_ data is available from the World Bank at https://datacatalog.worldbank.org/dataset/world-wind-speed-and-power-density-gis-data-2018; note that this data originally stems from http://globalwindatlas.info/.

Wind power density is a measurement used in the industry for evaluating the possible power from a particular site. It takes pressure into account, and is therefore generally more accurate than just raw wind speed data. I nevertheless have also included wind speed data in the map, and areas with very high wind speed appear with a slight white highlight.

I also use the _Stamen Terrain_ base map, rendered as greyscale, so that terrain/relief is visible. Terrain is an important factor in construction of wind power plants, as (obviously) it's harder to build on a massive slope, but mountains do have some of the highest wind speeds; it's a tradeoff.

### Transformations and Subsets

No major transformations are required for this data, aside from standard projection changes. The only subsetting required is to visually crop the map to focus on the US.

It should be noted that the wind turbine data is already a subset of sorts; it is a record of _wind power plants_, plants with a combined nameplate capacity of more than 1 megawatt. For this reason, it does not include small-scale wind turbines like school or farm installations; this is fine, since I'm interested in large-scale power for this map. It also does not use one point per actual wind turbine; multiple neighboring turbines typically belong to one single "plant".

### Analysis

The analysis for part 1 can be done purely visually. I did my analysis by looking at the map in QGIS; I'll note a few things I found interesting.

- Obviously, the majority of wind turbines are built on "blue" areas with more than 400 W/m² of power density. This isn't really surprising, but it's interesting to see how many turbines are scattered across the plains of the Midwest, or how they neatly cluster inside the blue areas in Texas.
- Few turbines are built on the actual mountain peaks, where there is high power density and very high raw wind speed (note the white highlight). I suspect this is because of construction difficulty, and also perhaps because this is protected parkland. In West Virginia, there are a few which come close to the peaks, probably because the Appalachians are shorter, and easier to build on.
- There are several clusters of turbines near Rhode Island and Maine, despite the power density not being very high at all. Not sure why they are building here; perhaps there is just high local demand for renewable energy, as this is the Northeast.
- I identified a few areas that would make sense for future expansion/development of turbines. **They are marked by black arrows.**
  - Western South Dakota and northwestern Nebraska seem to be well-suited for turbines, but are mostly empty at the moment. I'm not sure why; I don't see any Native American land when I zoom in, and the terrain is not particularly challenging. Perhaps there just isn't enough local demand for additional renewable power, with the population being so low. In any case, I suspect there may be future expansion here.
  - The Southern Appalachians also seem promising for future wind construction. They look roughly identical in wind power to the Northern Appalachians, which are already significantly developed, but are almost completely undeveloped right now. In addition, they are located nearby to population centers (or at least far closer than Nebraska and SD), so there is a customer base for power generation here.
- Though I didn't mark it, I noticed that wind power density on the Great Lakes is somewhat high. Offshore turbine construction on the lakes might be a worthwhile option, and indeed, some research confirms that [there is interest](https://www.greentechmedia.com/articles/read/mitsubishi-eyes-great-lakes-for-offshore-wind-development).

### Outputs

Part 1 yields one output, `Wind Turbines and Turbine Suitability.pdf`. Preview:
![image](https://user-images.githubusercontent.com/2071451/112563799-6ef14c00-8db0-11eb-8836-69520a29158b.png)
