Roman roads and settlements from https://itiner-e.org/

# get latest data
curl https://itiner-e.org/route-segments/download >all-routes.latest.geojson

# convert original ndjson to geojson
ndjson2geojson all-routes.latest.geojson >all-routes.latest.geosjon

# extract only Israel area
ogr2ogr -f GeoJSON routes-israel.geojson all-routes.latest.geosjon -spat 34.2 29.4 35.9 33.3

# extract only land routes
jq '{type:"FeatureCollection",features:[.features[]|select(.properties.type=="Main Road" or .properties.type=="Secondary Road")]}' routes-israel.geojson > routes-israel-land-only.geojson

# remove unneccessary properties for smaller file size
jq -c '{type:"FeatureCollection",features:[.features[]|{type:"Feature",geometry:.geometry,properties:{}}]}' routes-israel-land-only.geojson > routes-israel-land-only.minimized.geojson