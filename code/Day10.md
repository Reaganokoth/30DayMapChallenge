Day10
================
Anirudh Govind
(10 November, 2020)

## Get Data

``` r
# Load Bangalore wards

bangaloreWards <- read_sf(here::here("data/raw-data/bangaloreWardsUTM.shp"))

bangaloreWards <- bangaloreWards %>% 
  st_transform(3857)
```

``` r
# Load roads data (previously saved from OSM and cleaned up)

bangaloreRoads <- readRDS(here::here("data/derived-data/bangaloreRoads.rds"))

bangaloreRoads <- bangaloreRoads %>% 
  st_transform(3857)
```

## Wrangle Data

``` r
# Create boundary

vizBoundary <- bangaloreWards %>% 
  filter(ward_no == 168) %>% 
  st_centroid(.) %>% 
  st_buffer(1000, endCapStyle = "SQUARE")
```

    ## Warning in st_centroid.sf(.): st_centroid assumes attributes are constant over
    ## geometries of x

``` r
# Clip roads ot this boundary

vizRoads <- st_intersection(vizBoundary, bangaloreRoads)
```

    ## Warning: attribute variables are assumed to be spatially constant throughout all
    ## geometries

``` r
# Separate roads by type

vizPrimary <- vizRoads %>% 
  filter(highway == "primary")

vizSecondary <- vizRoads %>% 
  filter(highway == "secondary")

vizResidential <- vizRoads %>% 
  filter(highway == "residential")
```

## Build Map

``` r
# Put layers together

mapBoundary <- vizBoundary %>% 
  tm_shape() +
  tm_fill(col = "#000000") +
  tm_layout(bg.color = "white",
            frame = F,
            attr.outside = T,
            outer.margins = 0,
            asp = 0,
            scale = 0.8,
            main.title = "Bangalore: Jayanagar's Street Grid",
            main.title.color = "#000000",
            main.title.size = 1.75,
            main.title.fontface = 2,
            main.title.fontfamily = "Arial Narrow") + 
  tm_credits("#30DayMapChallenge | Day 10 | Anirudh Govind | Nov 2020\nMap data © OpenStreetMap contributors and available from https://www.openstreetmap.org",
             col = "#000000",
             size = 0.8,
             position = c("left", "bottom"),
             fontfamily = "Arial Narrow")

mapPrimary <- vizPrimary %>% 
  tm_shape() +
  tm_lines(col = "#ffffff",
           lwd = 3.6)

mapSecondary <- vizSecondary %>% 
  tm_shape() +
  tm_lines(col = "#ffffff",
           lwd = 3.2)

mapResidential <- vizResidential %>% 
  tm_shape() +
  tm_lines(col = "#ffffff",
           lwd = 2.8)

mapJayanagar <- mapBoundary + mapPrimary + mapSecondary + mapResidential
```

## Export Map

``` r
# Export the map as an image to upload onto twitter

tmap_save(tm = mapJayanagar,
          filename = here::here("exports/Day10.png"),
          dpi = 450,
          width = 200,
          height = 200,
          units = "mm")
```

    ## Map saved to G:\00_Git Repos\30DayMapChallenge\exports\Day10.png

    ## Resolution: 3543.307 by 3543.307 pixels

    ## Size: 7.874016 by 7.874016 inches (450 dpi)
