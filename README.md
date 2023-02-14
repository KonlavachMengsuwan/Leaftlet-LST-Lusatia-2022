# Leaftlet-LST-Lusatia-2022

# Leaflet 
## Lusatia
### LST

```
library(raster)
library(leaflet)
library(sf)
library(rgdal)
library(leaflet.opacity)
```
```
setwd("")

LST = raster("LST_2022_07_19_Landsat8L1_Lusatia_WGS84.tif")
plot(LST)
```
# Lusatia Boundary
```
setwd("")
Lusatia = readOGR("Lusatia_Administrative_Boundary_EPSG4326_Dissolved.shp")
```
# Districts Boundary
```
setwd("")

Bautzen = st_read("Bautzen.shp")
Cottbus = st_read("Cottbus.shp")
DahmeSpreewald = st_read("Dahme-Spreewald.shp")
ElbeElster = st_read("Elbe-Elster.shp")
Görlitz = st_read("Görlitz.shp")
OberspreewaldLausitz = st_read("Oberspreewald-Lausitz.shp")
SpreeNeiße = st_read("Spree-Neiße.shp")
```

# Mask Lusatia Districts
```
Bautzen_LST = raster::mask(LST, Bautzen)
Bautzen_LST = raster::crop(Bautzen_LST, Bautzen)

Cottbus_LST = raster::mask(LST, Cottbus)
Cottbus_LST = raster::crop(Cottbus_LST, Cottbus)

DahmeSpreewald_LST = raster::mask(LST, DahmeSpreewald)
DahmeSpreewald_LST = raster::crop(DahmeSpreewald_LST, DahmeSpreewald)

ElbeElster_LST = raster::mask(LST, ElbeElster)
ElbeElster_LST = raster::crop(ElbeElster_LST, ElbeElster)

Görlitz_LST = raster::mask(LST, Görlitz)
Görlitz_LST = raster::crop(Görlitz_LST, Görlitz)

OberspreewaldLausitz_LST = raster::mask(LST, OberspreewaldLausitz)
OberspreewaldLausitz_LST = raster::crop(OberspreewaldLausitz_LST, OberspreewaldLausitz)

SpreeNeiße_LST = raster::mask(LST, SpreeNeiße)
SpreeNeiße_LST = raster::crop(SpreeNeiße_LST, SpreeNeiße)
```

# Color Pallettes
```
pal <- colorNumeric(
  palette = rev("RdYlBu") ,
  domain = values(LST), na.color = "transparent",  reverse = TRUE)
```

# Leaflet Plot
```
LST_Lusatia = leaflet() %>%
  
  setView(lng = 14.332868, lat = 51.756310, zoom = 10) %>% 
  
  # Basemap (baseGroups)
  addTiles(group = "OSM (default)") %>%
  addProviderTiles(providers$OpenStreetMap, group = "Open Street Map") %>% 
  addProviderTiles(providers$Esri.WorldImagery, group = "ESRI World Imagery") %>% 
  addProviderTiles(providers$CartoDB.Positron, group = "CartoDB Positron") %>%
  
  
  # LST Raster
  addRasterImage(Bautzen_LST, colors = pal, opacity = 0.7, group = "Bautzen_LST") %>%
  addRasterImage(Cottbus_LST, colors = pal, opacity = 0.7, group = "Cottbus_LST", layerId = "raster") %>%
  addRasterImage(DahmeSpreewald_LST, colors = pal, opacity = 0.7, group = "DahmeSpreewald_LST") %>%
  addRasterImage(ElbeElster_LST, colors = pal, opacity = 0.7, group = "ElbeElster_LST") %>%
  addRasterImage(Görlitz_LST, colors = pal, opacity = 0.7, group = "Görlitz_LST") %>%
  addRasterImage(OberspreewaldLausitz_LST, colors = pal, opacity = 0.7, group = "OberspreewaldLausitz_LST") %>%
  addRasterImage(SpreeNeiße_LST, colors = pal, opacity = 0.7, group = "SpreeNeiße_LST") %>%
  
  # Lusatia Boundary
  addPolylines(data = Lusatia, color = "red", weight = 5, opacity = 0.5, group = "Lusatia") %>%
  
  # Legend
  addLegend(pal = pal, 
            values = values(LST),
            title = "Land Surface Temperature (C) <br> 2022_07_19", opacity = 0.9) %>%
  
  # Layer Control
  addLayersControl(baseGroups = c("OSM (default)", "ESRI World Imagery", "CartoDB Positron"),
                 overlayGroups = c("Bautzen_LST",
                                   "Cottbus_LST",
                                   "DahmeSpreewald_LST",
                                   "ElbeElster_LST",
                                   "Görlitz_LST",
                                   "OberspreewaldLausitz_LST",
                                   "SpreeNeiße_LST",
                                   "Lusatia"
                 ),
                 
                 options = layersControlOptions(collapsed = FALSE)) %>%
  
  
  # Hide Group
  hideGroup("Bautzen_LST") %>% 
  hideGroup("Cottbus_LST") %>% 
  hideGroup("DahmeSpreewald_LST") %>% 
  hideGroup("ElbeElster_LST") %>% 
  hideGroup("Görlitz_LST") %>% 
  hideGroup("OberspreewaldLausitz_LST") %>% 
  hideGroup("SpreeNeiße_LST") %>%
  
  # Opacity
  addOpacitySlider(layerId = "raster")
```

```
LST_Lusatia
```
```
setwd("")
htmlwidgets::saveWidget(LST_Lusatia, "Lusatia_LST.html", selfcontained = F)
```
