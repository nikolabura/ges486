# Final Project

For my final project, I created a web app called FCCLE, the FCC Location Explorer (pronounced "fickle"; it's a little contrived, but it works).
The aim of this project was to load in data from the FCC Universal Licensing System database (specifically microwave radio data),
reprocess it into a useful format, and display it to be explored and interpreted in an interactive way online.

First things first. The web app can be accessed [via this link](http://umbcsad.crabdance.com/), or by clicking the logo:

[<img src="https://user-images.githubusercontent.com/2071451/119211568-de1db080-ba80-11eb-88b8-1544356269b4.png">](http://umbcsad.crabdance.com/)

For an explanation of how to use the app, please see the Usage section below.

Finally, **source code is available at [https://github.com/nikolabura/fccle](https://github.com/nikolabura/fccle).**

# [2] Write-Up

### Microwave Towers

The main goal of this project was to analyze FCC records of microwave towers and microwave paths.
[Microwave radio](https://en.wikipedia.org/wiki/Microwave_transmission), on the large/outdoor scale, is commonly used for _point-to-point_ communications, meaning that as opposed to traditional omnidirectional broadcasting, there is a transmitter antenna, a reciever antenna, and a straight line-of-sight path between the two.

In the US, these antenna and paths are registered with the FCC, which makes them available for download through the [ULS database weekly export](https://www.fcc.gov/uls/transactions/daily-weekly#fcc-uls-transaction-files-weekly).

Common uses of microwave links include [cellular network backhaul](https://en.wikipedia.org/wiki/Backhaul_(telecommunications)) (when it's too expensive or remote to run a fiber optic cable all the way to the cell site), [internal data transfer](https://en.wikipedia.org/wiki/Cable_television_headend) for television companies, linking [TV and radio studios](https://en.wikipedia.org/wiki/Studio_transmitter_link) to mountaintop transmitters, extremely-low-latency communications for [high-frequency-trading firms](https://www.bloomberg.com/news/features/2019-03-08/the-gazillion-dollar-standoff-over-two-high-frequency-trading-towers), and backup communications for emergency situations.

<img align="right" style="margin: 5px" src="https://cdn.discordapp.com/attachments/552980096315686955/845486202828095508/20210520_183217_HDR.jpg" width="120px">

Although maps of this microwave tower and microwave path data exist already (one of the best I've found is https://maprad.io/, though to be honest, I didn't know it existed until I had already started the project, so that was a bit depressing), they're a bit limited in scope. Most either require really specific queries and a lot of clicking around, or they show you all the links/paths at once, and it quickly becomes a bit of an unreadable mess of lines (seems the airwaves are crowded). This project aims to present the data in a more useful and accessible way.

To the right is an image of the UMBC campus' radio tower. One microwave antenna is easily visible on the side (it's the white circle), seemingly pointed in the direction of the PAHB.

### Paging Towers

The FCC actually publishes a wide variety of databases; microwave represents just one of several export options. In the interest of variety, I also downloaded and processed the database of paging towers. (Hey, pagers are still a thing!) The main focus of the project, however, is on the microwave links.

# Usage

To use [the web app](http://umbcsad.crabdance.com/)), pan to the area you wish to analyze, zoom in on it, and click the **Load Microwave Towers in Area** button. The map will be populated with clusters representing microwave towers. You can click on the clusters to zoom in on them; though, some represent co-located antennas on the same tower, so zooming won't help there.

You can click on an individual antenna (blue marker) to view its callsign and **total height**. The popup also gives you a **Path Trace** button. If you click it, the app will contact the server, check if there are any registered paths associated with that particular antenna, and draw them out.

This is kind of a pain to do, so the **Path Trace all in Cluster** button is offered. Click it, then click on one of the antenna clusters on the map, and the client will automatically do a path trace on _all_ of them, drawing out any paths which are found. Be aware that this can take a while to complete if there's a lot of paths, especially due to the feature discussed in the next paragraph. The counter next to the button displays the current amount of pending requests, once it ticks down to zero and the activity indicator box quiets down, it's finished.

Anyways, drawing paths is neat and all, but there's nothing particularly special about it. The real aim of this project was to "propagate" paths, and attempt to discover and draw an entire regional network. That is, if you click on Tower A, it will draw out a link to Tower B; the client then recursively issues another request for all of Tower B's paths, which links it to Tower C, then Tower C links to Tower D, and so on.

In practice, to be honest, this propagation feature doesn't work all that well. The data is just a bit too spotty, and I suppose microwave networks might be somewhat of a [relic of the past](https://hackaday.com/2017/07/10/horns-across-america-the-att-long-lines-network/) - perhaps they're not really used for that purpose today. However, when it does work, I think it's pretty neat, and I'll show some examples below.

In the upper-right corner, you can access the **Layers control** and switch between different basemaps.

- Positron: The default basemap, useful for map aesthetic and contrast against the markers and paths.
- Terrain: Useful for finding mountaintop sites and understanding how topography plays a role in wireless network design (microwave links are line-of-sight).
- Satellite: ESRI World Imagery photos.
- OpenStreetMap: Just OSM
- Microwave Tower Dots: A WSM map of the microwave tower data, rendered by a Geoserver install on the backend. Very useful for large-scale viewing.

Finally, you can also load in pager antenna locations by clicking the **Load Paging Towers in Area** button. You can click on each paging tower to view the frequency it uses.

# [3,4] Analysis and Reproducibility

My FCC data was sourced from [here](https://www.fcc.gov/uls/transactions/daily-weekly). I used the following ZIP files:

- Microwave: `ftp://wirelessftp.fcc.gov/pub/uls/complete/l_micro.zip`
    - Within Microwave, I made use of `LO.dat` for locations and `PA.dat` for paths.
- Paging: `ftp://wirelessftp.fcc.gov/pub/uls/complete/l_paging.zip`
    - Within Paging, I made use of `LO.dat` for locations and `EM.dat` for "emissions" (frequency).

The FCC data is distributed as archives full of `.dat` files. They are text files, and are pipe-delimited - yes, really, like the `|` character. Weird choice, but sure. Documentation is available [here](https://www.fcc.gov/wireless/data/public-access-files-database-downloads).
[This PDF](https://www.fcc.gov/sites/default/files/pubacc_tbl_abbr_names_08212007.pdf) lists the meaning of each acronym. The one that's most important to us is the `LO` file, which indicates the geographic location of each antenna site. It's in NAD83, Degrees Minutes Seconds form, with the D, M, S, and direction being in separate fields.
I wrote a Python script to process an LO file and tack on a decimal degrees field; it's available in `utils/process_fcc_dat_to_coords.py`.
Then, I imported the file into QGIS using Delimited Text Import.

![image](https://user-images.githubusercontent.com/2071451/119213113-e7ac1600-ba8a-11eb-9462-d50f0713de30.png)

I re-exported it as a Geopackage, since the delimited text format was pretty slow. From here, the question is, how to get it onto a webpage? QGIS2Web would be infeasible, since representing all this data (1,581,932 rows), even in
a compressed format, would quickly overload the browser - trying to export it all as GeoJSON would be beyond infeasible. Even a clipped export of just the Maryland area was simply too much.

I spun up a Geoserver installation on my server and imported the Geopackage to try and serve it as WMS, but that's a tiled/raster output, so not really super useful
(it does still live on in the final product through the Microwave Tower Dots basemap).
In the end, I settled on a PostGIS database. You can log in to it directly in QGIS and upload the data there, but I found it was significantly faster to `apt install gdal-bin` and use `ogr2ogr` for the import: `ogr2ogr -f PostgreSQL "PG:user=postgres password=[redacted] dbname=fccle" whole_US_dd_micro_LO.gpkg`.

With the data now in PostGIS, I wrote a backend in Python Flask with some API calls to return GeoJSON for specific map bounds. Thanks to some amazing PostGIS features, this is [easy enough](https://gis.stackexchange.com/questions/112057/sql-query-to-have-a-complete-geojson-feature-from-postgis):

```sql
SELECT jsonb_build_object(
    'type',     'FeatureCollection',
    'features', jsonb_agg(features.feature)
)
FROM (
    SELECT jsonb_build_object(
        'type',       'Feature',
        'id',         fid,
        'geometry',   ST_AsGeoJSON(geom)::jsonb,
        'properties', to_jsonb(inputs) - 'gid' - 'geom'
    ) AS feature
    FROM (
        SELECT *
        FROM public.dd_micro_lo
        WHERE ST_Intersects(ST_MakeEnvelope(%s,%s,%s,%s, 4326), geom)
        LIMIT 4000
    ) inputs
) features;
```

``` python
cur = conn.cursor()
cur.execute(sql, (swlon, swlat, nelon, nelat))
```

For the frontend, I made use of the Leaflet library and its [Marker Clustering Plugin](https://github.com/Leaflet/Leaflet.markercluster).
I wrote some code to connect to the backend...

```js
fetch("/api/microwave_towers?swlat=" + swlat + "&swlon=" + swlon
    + "&nelat=" + nelat + "&nelon=" + nelon)
```

... and fetch the microwave tower JSON. It later checks incoming feature IDs to prevent duplication.

Fetching the paths for a given tower is also done with SQL. In these FCC datasets, individual antennas are identified by a combination of the _callsign_ and the _location number_ (or at least that's the theory - I found that often the data was messier than that).

```sql
SELECT * FROM fccle.microwave_paths m
WHERE
    (m.callsign = %s AND m.transmit_location_number = %s)
    OR (m.receiver_callsign = %s AND m.receiver_location_number = %s)
```

Finally, propagation/recursion - drawing links from Tower A to B to C to D, etc. - is all done on the client side, within the `pathTrace` function of `fccle.js`.
This approach is hugely inefficient, to be honest. A far better approach would be to run it on the server, either in Python or as a recursive query in SQL (the latter option could probably even give near-instantaneous results given PostgreSQL's efficiency).
This is definitely a good thing to optimize in the future.

For my basemaps, I made use of Stamen Terrain, CartoDB's Positron map, ESRI's World Imagery, and openstreetmap.org's default Mapnik layer. These are visible near the top of `fccle.js`.

# [5] Final Output

I suppose my final output is the web map / web app itself.

If a static map is desired, I used the Export buttons to export the GeoJSON for some microwave paths, and moved them into QGIS. I acquired Frederick DEM (elevation) data from USGS AρρEEARS, and ran the QGIS Viewshed tool to get the viewshed (points visible as line-of-sight) for the peak of Gambrill Mountain, where a lot of microwave towers are located. In the map below, the bright areas are within the viewshed; the dark areas are outside of it.

![viewshed](https://user-images.githubusercontent.com/2071451/119213675-eda3f600-ba8e-11eb-854e-d599bcaef86e.png)

It's cool to see how, sure enough, the microwave links almost entirely go to sites within the viewshed. The colored elevation overlay also shows how they link from one mountaintop to the other.

# [6] Maps

Here are some selected maps I made using the web app; I found them interesting.

## Microwave Networks

![hagerstown](https://user-images.githubusercontent.com/2071451/119214044-b84cd780-ba91-11eb-9f6d-cf240f956f39.png)

Above, I ran Path Trace on the Hagerstown cluster and after propagating for a while, it yielded this pretty cool-looking result.
This is one of the more proper-looking multi-node "networks" I've managed to get the app to discover.

![mountaintop](https://user-images.githubusercontent.com/2071451/119214098-22fe1300-ba92-11eb-882d-dc3857bf1906.png)

Above, another trace that I ran from that mountaintop next to the "86". I've found that running mass-path-traces from mountaintop sites seems
to have the most successful and interesting results.
I think that cities are covered in a lot of (subjectively) uninteresting short-range links while mountaintops have the long-range stuff.

![elrama](https://user-images.githubusercontent.com/2071451/119214132-648ebe00-ba92-11eb-94f4-7b5aec263b3d.png)

Speak of the devil: Above, next to this factory/plant in Elrama, a microwave link is used short-range to cross a river. Cheaper than a cable, I assume.

## UMBC Campus Tower

![image](https://user-images.githubusercontent.com/2071451/119214186-dbc45200-ba92-11eb-9e73-9245c8c10857.png)

The UMBC radio tower (pictured in "Write Up" above) links to Gambrill Mountain in Frederick, callsign `WQML415` in DC (owned [by BGE](https://wireless2.fcc.gov/UlsApp/UlsSearch/licenseAdminSum.jsp?licKey=3227469)), and a site in nearby Baltimore.

## High-Frequency Trading

_Note: I updated the database with data for the whole nation, so we can do this analysis now._

HFT famously makes use of microwave links for very low-latency (and therefore very lucrative) connections to stock exchanges.

The NASDAQ stock exchange data center is pretty hard to find, probably by design. We know it's [in Carteret, NJ](https://www.sec.gov/comments/4-729/4729-8131081-226476.pdf), but where, exactly? [Incredibly unreliable sources off of Google](http://wikimapia.org/27502149/NASDAQ-Carteret-Data-Center) seem to all point to this one building off the Turnpike, and sure enough, it's got antennas:

![nasdaq_1](https://user-images.githubusercontent.com/2071451/119214276-8b012900-ba93-11eb-83f1-fffad33f65e1.png)
![nasdaq_4](https://user-images.githubusercontent.com/2071451/119214279-93596400-ba93-11eb-9543-146a17509fc0.png)

It's got links, too:

![nasdaq_2](https://user-images.githubusercontent.com/2071451/119214286-a53b0700-ba93-11eb-8729-79d2f76af8e7.png)

One of these goes to an Equinx data center much closer to NYC:

![nasdaq_5](https://user-images.githubusercontent.com/2071451/119214313-e7644880-ba93-11eb-8310-efd4fdeb6857.png)

But, unfortunately I wasn't able to get anything super interesting out of it. I did a few manual path traces, thinking maybe different companies (and therefore different callsigns) own different parts of the relay network, and I did manage to trace a path partway to Chicago:

![nasdaq_3](https://user-images.githubusercontent.com/2071451/119214307-dc111d00-ba93-11eb-9c02-154c499310f4.png)

But, for all I know, this could be total placebo. I'm not even sure if this is the right building. Still, it's pretty neat!

## Government Use of Microwave Links

Everything we've looked at so far has been mostly commercial. So I also checked out the Mount Weather Emergency Operations Center, a semi-famous underground FEMA site built into a mountain in Virginia. (How spooky.) It's got towers:

![image](https://user-images.githubusercontent.com/2071451/119214367-58a3fb80-ba94-11eb-95f5-b253433e04a4.png)

And what about links?

![image](https://user-images.githubusercontent.com/2071451/119214405-a6206880-ba94-11eb-99ff-cf0906e949db.png)

Upon closer inspection, paths were found to Harrisonburg (blue marker) via Mt Hogback (6), Clear Spring (5), and an AT&T [Project Office](https://en.wikipedia.org/wiki/Project_Offices) (2) by Short Hill Mountain. The (2) on the right near DC is the [Tysons Corner Communications Tower](https://en.wikipedia.org/wiki/Tysons_Corner_Communications_Tower), but why isn't it directly linked?

![image](https://user-images.githubusercontent.com/2071451/119214486-42e30600-ba95-11eb-8dc0-caa45d63af22.png)

The Tysons Corner Tower was discovered by the propagator since both it and Mt Weather apparently link to a site in Puerto Rico.
I didn't even know microwave links can go that far; the Earth's curvature would definitely interfere at that point.
I kind of assume this is a misplacement / glitch in the FCC data, but how weird.

## QGIS: Heatmap

I did some analysis of the original tower location data in QGIS (no paths).

Heatmapping worked, but wasn't really interesting at all; pretty much just a population map. Though it _is_ cool to note how bright New York is compared to other cities - I wonder if that's just because it's bigger and denser, or if it's an indicator of stock market/HFT presence?

![image](https://user-images.githubusercontent.com/2071451/119214569-d1f01e00-ba95-11eb-80dd-b016f497463b.png)

The heatmap does reveal an interesting "gap" in West Virginia, though. That spot is one of the furthest places on land from any microwave towers in the whole Eastern US (the West has plenty of gaps as it is more sparse in general). What's up with that?

As it turns out, it's not just because of low population density! That gap is just a few miles from the [Green Bank Telescope](https://en.wikipedia.org/wiki/Green_Bank_Telescope), and is located very close to the center of the [National Radio Quiet Zone](https://en.wikipedia.org/wiki/United_States_National_Radio_Quiet_Zone).

# [7] Data

- Microwave: ftp://wirelessftp.fcc.gov/pub/uls/complete/l_micro.zip
- Paging: ftp://wirelessftp.fcc.gov/pub/uls/complete/l_paging.zip
- DEM for Viewshed: https://lpdaacsvc.cr.usgs.gov/appeears/explore
- Basemaps:
    - Stamen Terrain: https://stamen-tiles-{s}.a.ssl.fastly.net/terrain/{z}/{x}/{y}{r}.{ext}
    - CartoDB Positron: https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png
    - Esri WorldImagery: https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}
    - OpenStreetMap Mapnik: https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png

Source code is available at [https://github.com/nikolabura/fccle](https://github.com/nikolabura/fccle).

# Discussion & Future Work

I'm pretty happy with this project, but it does have some flaws that could be addressed.

- Optimization of the "propagator" / path tracer. Just run it on the server and performance would likely improve a lot. I do kind of enjoy watching it slowly reveal paths one-by-one, though - reminds me of those hacking scenes in movies. :)
- More data visible per each tower or link, particularly, ownership data. I was going to import the `EN.dat` ("entity") file which contains this info, and add an API call for it, but didn't have time.
- Maybe some search or filtering options - show only towers and paths owned by some company, show only towers taller than _x_ meters, etc. (The former would require ownership data to be set up, obviously.) Then, this would be easy enough to implement on the client side with JS, or on the server with a SQL WHERE statement.

![image](https://user-images.githubusercontent.com/2071451/119215485-f4853580-ba9b-11eb-8f62-f8465ba6b14b.png)
