# Final Project

For my final project, I created a web app called FCCLE, the FCC Location Explorer (pronounced "fickle"; it's a little contrived, but it works).
The aim of this project was to load in data from the FCC Universal Licensing System database (specifically microwave radio data),
reprocess it into a useful format, and display it to be explored and interpreted in an interactive way online.

First things first. The web app can be accessed [via this link](http://umbcsad.crabdance.com/), or by clicking the logo:

[<img src="https://user-images.githubusercontent.com/2071451/119211568-de1db080-ba80-11eb-88b8-1544356269b4.png">](http://umbcsad.crabdance.com/)

For an explanation of how to use the app, please see the Usage section below. Source code is available at https://github.com/nikolabura/fccle.

## [2] Write-Up

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

## Usage

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

Finally, you can also load in pager antenna locations by clicking the **Load Paging Towers in Area** button.

## [3,4] Analysis and Reproducibility

Firstly, the FCC data is distributed as archives full of `.dat` files. They are text files, and are pipe-delimited - yes, really, like the `|` character. Weird choice, but sure. Documentation is available [here](https://www.fcc.gov/wireless/data/public-access-files-database-downloads).
[This PDF](https://www.fcc.gov/sites/default/files/pubacc_tbl_abbr_names_08212007.pdf) lists the meaning of each acronym. The one that's most important to us is the `LO` file, which indicates the geographic location of each antenna site. It's in NAD83, Degrees Minutes Seconds form, with the D, M, S, and direction being in separate fields.
I wrote a Python script to process a LO file and tack on a decimal degrees field; it's available in `utils/process_fcc_dat_to_coords.py`.
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
