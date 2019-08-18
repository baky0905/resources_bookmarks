## KEY LEARNINGS

#### Setting Up Interactive Web Maps

**leaflet**

+  open-source JavaScript library
+ popular option for creating interactive mobile friendly maps
+ can be created usingonly R code via the htmlwidgets package

**leaflet Provider List**

- The `leaflet` packages comes with 100+ provider tiles
- The names of these tiles are stored in a list named `providers` 
  - names(providers)

**addProviderTiles()**

- Replace `addTiles()` with `addProviderTiles()` to change your basemap
- Pass name of provider tile to `addProviderTiles()`

```r
leaflet() %>% 
     # addTiles() 
     addProviderTiles("OpenStreetMap.BlackAndWhite")
```

names(providers)[str_detect(names(providers), "CartoDB")]



**Default map view**

Map with CartoDB tile centered on DataCamp's NYC office with zoom of 6

```
leaflet()  %>% 
    addProviderTiles("CartoDB")  %>% 
    setView(lng = -73.98575, lat = 40.74856, zoom = 6)
```

```R
leaflet(options = leafletOptions(
                    # Set minZoom and dragging 
                    minZoom = 12, dragging = TRUE))  %>% 
  addProviderTiles("CartoDB")  %>%
```

Set default zoom level 

```R
  setView(lng = dc_hq$lon[2], lat = dc_hq$lat[2], zoom = 14) %>% 
```

Set max bounds of map 

```R
  setMaxBounds(lng1 = dc_hq$lon[2] +  .05, 
               lat1 = dc_hq$lat[2] + .05, 
               lng2 = dc_hq$lon[2] -  .05, 
               lat2 = dc_hq$lat[2] - .05) 
```

**Cleaning up base map**

Remove markers, reset bounds, and store the updated map in the m object
map_clear <- map  %>%
        clearMarkers()  %>% 
        clearBounds()

**Creating a Color Palette using colorFactor**

With `colorFactor()` we can create a color palette that maps colors the levels of a factor variable.

```
pal <- 
   colorFactor(palette = c("blue", "red", "green"), 
               levels = c("Public", "Private", "For-Profit"))

m %>% 
    addCircleMarkers(color = ~pal(sector_label))
```

Why might we not want to use this particular color palette?

If you are interested in using a continuous variable to color a map see `colorNumeric()`.

```
pal <- colorNumeric(palette = "RdBu", domain = c(25:50))

ipeds %>% 
    leaflet() %>% 
        addProviderTiles("CartoDB")  %>% 
        addCircleMarkers(radius = 2, color = ~pal(lat))
```

**Add a legend that displays the colors used in pal**

m %>% 
    addLegend(pal = pal, 
              values = c("Public", "Private", "For-Profit"))



#### Groups, Layers, and Extras

```
library(leaflet.extras)

leaflet() %>%
  addTiles() %>% 
  addSearchOSM() %>%
  addReverseSearchOSM()
```

Create data frame called private with only private colleges

```R
private <- filter(ipeds, sector_label == "Private")  
```

Add private colleges to `m3` as a new layer

```R
m3 <- m3 %>% 
        addCircleMarkers(data = private, radius = 2, label = ~htmlEscape(name),
                         color = ~pal(sector_label), group = "Private") %>% 
        addLayersControl(overlayGroups = c("Public", "Private"))

m3
```

```R
leaflet() %>% 
#Add the OSM, CartoDB and Esri tiles
  addTiles(group = "OSM") %>% 
  addProviderTiles("CartoDB", group = "CartoDB") %>% 
  addProviderTiles("Esri", group = "Esri") %>% 
#Use addLayersControl to allow users to toggle between basemaps
  addLayersControl(baseGroups = c("OSM", "CartoDB", "Esri"))
```

```R
#Make each sector of colleges searchable 
m4_search <- m4  %>% 
        addSearchFeatures(
            targetGroups = c("Public", "Private", "For-Profit"), 
            # Set the search zoom level to 18
            options = searchFeaturesOptions(zoom = 18)) 
#Try searching the map for a college
m4_search
```

**Cluster all colleges using** `clusterOptions`

```R
addCircleMarkers(clusterOptions = markerClusterOptions())
```



#### Plotting Polygons

**Introduction to Spatial Data**

We have been mapping points, but there are several spatial features that can be mapped, including polygons. In R, polygons are often stored in a SpatialPolygonsDataFrame that holds the polygon, coordinate information, and a data frame with one row per polygon.

A SpatialPolygonsDataFrame called `shp` that contains the zip code boundaries for North Carolina has been loaded for you. `shp` has five slots that store various types of information:

1. **data**: data associated with each polygon
2. **polygons**: coordinates to plot polygons
3. **plotOrder**: order in which polygons are plotted
4. **bbox**: bounding box for geographic data (i.e., a rectangle)
5. **proj4string**: coordinate reference system



**addPolygons()**

+ weight - thickness of the boundary lines in pixels
+ color - the color of the polygons
+ label - the information to appear on hover
+ highlight - options to highlight a polygon on hover

**Color Numeric Data**

`colornumeric()`

+ colorNumeric 
+ colorBin
+ colorQuantile

example for colorNumeric()

```
nc_pal <- colorNumeric("Blues", domain = high_inc@data$mean_income)

previewColors(pal = nc_pal, values = c(seq(100000, 600000, byt = 100000)))
```

