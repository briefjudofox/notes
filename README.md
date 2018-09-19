# Append WKT and geojson 

Load a file (shp, geojson, csv, etc.) with geom append WKT and geojson format for use in hive, presto, etc.

```python
import geopandas as gpd
import fiona
import geojson
import shapely.wkt

# Read file
geoms = gpd.read_file('us_hoods.geojson')

# Add geojson formatted geometry (in addition to WKT)
geo_json_geom = []
for index, row in geoms.iterrows():
    geojson_geom = geojson.Feature(geometry=row['geometry'], properties={})
    # Only set geometry, not feature for ease of use on Qubole
    geo_json_geom.append(geojson_geom['geometry'])

# Append geojson to new column on dataframe
geoms['geometry_geojson'] = geo_json_geom

# Rename geometry column (WKT) for clarity:
geoms.rename(index=str, columns={"geometry": "geometry_wkt"}, inplace=True)

# Export as CSV for Hive import, etc
geoms.to_csv('features.csv',index=False, header=False, sep='|', quoting=3)
```
