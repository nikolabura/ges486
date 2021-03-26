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

## 2 - Energy Mix Throughout the Year

### Topic

The _energy mix_ describes the proportion of electricity which is provided by a particular energy source. Of particular interest, for obvious environmental reasons, is the mix between renewable and nonrenewable energy. Several countries have achieved 100% renewable energy days, but it is usually sporadic.

Here, I'll try to investigate how the energy mix changes for US states over the course of a year (2019). For example, I want to see if some states fall back to nonrenewable energy during winter, or whether AC usage in summer or electric heating in winter creates high demand, requiring the use of nonrenewable power or even natural gas peaker plants.

My personal hypothesis is that the proportion of nonrenewable energy will become higher during winter.

### Data

The only data source needed here is the electric generation totals (in megawatthours), per each state, per each month, per each type of energy source. The USA Energy Information Administration once again provides this data, at https://www.eia.gov/electricity/data/state/ (see _Net Generation by State by Type of Producer by Energy Source_).

For future research, it might be interesting to visualize some of the other data sources here, such as _Fossil Fuel Consumption for Electricity Generation by Year, Industry Type and State_. I will not do so for this project, though.

### Transformation and Subset

For processing of the data, I used LibreOffice Calc, as it is provided in XLSX format. I took the 2018 sheet and created a pivot table, allowing me to subset for _Total Electric Power Industry_ (not heating, etc), group by state, and sum up multiple generation sources by checkbox.

![image](https://user-images.githubusercontent.com/2071451/112567082-94815400-8db6-11eb-89e8-ad5cfd49c09f.png)

#### Preliminary Analysis

To get started, I used the pivot table to grab just Maryland's data for Coal vs Natural Gas, and found that there was already a visible pattern.

![image](https://user-images.githubusercontent.com/2071451/112566162-d7dac300-8db4-11eb-94cf-ea6d3bd7f6b2.png)

Natural gas really takes off in summer. I suspect this is due to operation of natural gas peaker plants to satisfy sporadic demand from people running AC, but I'm really not sure! In any case, it's good to see that there are some trends. It is disappointing to see that the total fossil-fuel fraction is relatively constant throughout the year, though.

#### Joining

I then used the pivot table to isolate out values and sums for a few different energy sources - coal, gas, the set of renewables, solar, wind... I copy-pasted each column over to a new spreadsheet.

![image](https://user-images.githubusercontent.com/2071451/112570907-4a4fa100-8dbd-11eb-821a-b5390ac7c4ff.png)

In QGIS, I used _Join attributes by field value_ and specified one-to-many, creating 12 State polygons for each state. Finally, I used Temporal filtering to assign each state polygon a datetime based on its Month value. Now we can make temporal animations.

### Analysis

See above for the preliminary analysis that I did on the Maryland data.

I will discuss some analysis in the output section below.

### Outputs

I created several animated maps using this data, mostly choropleths.

**Renewables proportion over the year:**

![renewables_time](https://user-images.githubusercontent.com/2071451/112573371-89342580-8dc2-11eb-9c7d-8c242ecba80d.gif)

As I predicted with the MD graph, the change in renewables production throughout the year seems pretty low. It seems like the renewables fraction actually intensifies a bit in later summer - perhaps that's solar at work? Let's check.

**Solar proportion:**

![solar](https://user-images.githubusercontent.com/2071451/112574166-26dc2480-8dc4-11eb-97cd-2c47eeaba126.gif)

Solar definitely does intensify in summer for some states, mostly in the southwest. I wonder if this is due to them using thermal "mirror" plants instead of photovoltaic, so it's more dependent on actual heat. The data doesn't differentiate thermal vs. photovoltaic.

Let's also check wind, though.

**Wind proportion:**

![wind](https://user-images.githubusercontent.com/2071451/112574392-89352500-8dc4-11eb-8c90-c12c980a60a3.gif)

Wind is kind of sporadic. It definitely does seem to intensify a bit later in the summer, in the northern states.

**Natural gas proportion over the year:**

And finally:

![natgas](https://user-images.githubusercontent.com/2071451/112573841-7a9a3e00-8dc3-11eb-8a7d-3b8e4cecbe5c.gif)

I'm pretty happy with this one. You can really see how the gas-fired peaker plants start to come online later in the summer as more people start using AC - or that's my theory, at least.
