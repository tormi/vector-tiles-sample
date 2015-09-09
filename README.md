# Custom vector tiles from Georaphy Class example made with MapBox Studio / TileMill2

This repo documents results of tests on vector tiles as defined by MapBox and shows various variants for how to use these tiles on your own server.

The repo contains two MapBox Studio projects: data source (.tm2source) and style (.tm2) necessary to create custom vector tiles (protobuf encoded MVT - MapBox Vector Tiles) packed in MBTiles and show what can be done with such tiles.
Both these projects contains the Mapnik's XML style which is usable with any mapnik-powered software, if mapniks is compiled together with [mapnik-vector-tiles](https://github.com/mapbox/mapnik-vector-tile).

The look&feel of this sample is a port of GeographyClass example from Tilemill into TileMill2 aka MapBox Studio.
It usesvector data (.shp) are from NaturalEarthData.com, originally distributed by MapBox with the open-source TileMill project for use in the Geography Class example: https://github.com/mapbox/tilemill/tree/master/examples/geography-class

## Work with MapBox Studio

- Clone this repo or [download](https://github.com/klokantech/vector-tiles-sample/archive/master.zip) the files to you computer.
- Start MapBox Studio (https://www.mapbox.com/mapbox-studio/)
- Click on Projects -> Browse and add the two projects
- Play with the styles in CartoCSS when the style project is openned
- Click on Layers -> Change source and assing your own local copy of Countries.tm2source from your disk
 
## Create vector MBTiles (with pbf inside)
- Open under "Sources" the source project Countries
- Check the layers on the right side and their definition. Feel free to add another shapefiles or Postgres requests.
- Click on "Settings" and "Export to MBTiles"

You will get a file with extension .mbtiles, which contains "pbf" vector tiles.
Such sample file can be also directly [downloaded as countries.mbtiles](https://github.com/klokantech/vector-tiles-sample/releases/download/v1.0/countries.mbtiles)

## Host the vector tiles on your own server

It is easy to host the vector tiles on MapBox service, but if you want it on your own server
is straightforward as well by installing [Tileserver-PHP project](https://github.com/klokan/tileserver-php/).
If you a have an Apache+PHP hosting - just unpack the project files and drop the MBTiles with vector (or raster) tiles in the same directory. See this video tutorial: https://youtu.be/F6MvDvc5m-I?list=PLGHe6Moaz52PiQd1mO-S9QrCjqSn1v-ay

Alternativelly you could also start the TileServer-PHP on your laptop with docker easily. The ready to use container is on [DockerHub](https://hub.docker.com/r/klokantech/tileserver-php/).
Under Mac OS X and Windows docker is extremely easy to use with the Kitematic graphical user interface: https://kitematic.com/
In Kitematic just search for "tileserver" and start the virtual machine with the project inside by clicking on "Start". Then click on "Volumes" and add in the folder your MBTiles files - and you get a running hosting for your vector tiles.

## Load the tiles into MapBox Studio from your server

MapBox Studio project with styles can load the vector tiles from your own server, if you provide URL to the TileJSON (linked from bottom of the sidebar in TileServer.php viewer of each layer). (Note: in MapBox Studio 0.2.7 is a bug [#1288](https://github.com/mapbox/mapbox-studio/issues/1288) with "Invalid URL", fixed in the master so probably fixed 0.2.8+).

There must be correct CORS headers on your server - but TileServer-PHP project ensures everything is fine out of the box.

## Host the vector tiles without any server at all

The vector tiles can be unpacked from MBTiles (SQLite) container and hosted just a in direct folder structure - the same way as raster tiles are typically made with a software like [MapTiler](http://www.maptiler.com) or GDAL2Tiles. The demonstration of such approach is visible at http://klokantech.github.io/mapbox-gl-js-offline-example/.

To unpack and ungzip the tiles I have used [mb-util](https://github.com/mapbox/mbutil):

    ./mb-util --image_format=pbf countries.mbtiles countries
    gzip -d -r -S .pbf *
    find . -type f -exec mv '{}' '{}'.pbf \;

## Display the vector tiles directly with MapBox GL JS

I have prepared a sample viewer with local copy of MapBox GL JS and custom tiles hosted as static files in here:

http://klokantech.github.io/mapbox-gl-js-offline-example/

The styling must be done in a JSON format manually - and is not exportable from MapBox Studio (yet).
Because of WebGL browser security restrictions, the tiles must be hosted in the same domain or you have to ensure [CORS](http://enable-cors.org/) are set. BTW GitHub Pages used for hosting above does not support CORS headers unfortunatelly.

For more details see: https://github.com/klokantech/mapbox-gl-js-offline-example

## Generate standard raster MBTiles with PNG / JPEG from MapBox Studio

MapBox Studio does not offer export of standard raster tiles, but it does contain mapnik which runs on localhost - so that people can preview the map. This is exposed also outside of the app [#1024](https://github.com/mapbox/mapbox-studio/issues/1024).
This means a basic program can request the raster tiles in a batch and save these into MBTiles.
If you start MapBox Studio - you can View -> Toggle Developer Tools and check Network request to the tiles you see in the project you edit.

Then a utility like [tilecloud](https://github.com/twpayne/tilecloud) can be started on command line to request all tiles at given resolution and save these into raster MBTiles format.

    $ ./tc-copy -v -o -b 0/0/0:6/*/* "http://localhost:3000/style/%(z)d/%(x)d/%(y)d.png?id=tmstyle:///Users/klokan/Documents/MapBox/project/countries.tm2" countries-raster.mbtiles





