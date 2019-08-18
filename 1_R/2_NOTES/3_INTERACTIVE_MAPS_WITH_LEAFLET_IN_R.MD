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

#### Plotting Points



#### Groups, Layers, and Extras



#### Plotting Polygons