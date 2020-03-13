# Packages
###############################################################################################################################
library(dplyr)
library(readr)
library(jsonlite)
library(lubridate)
library(sf)
library(geojsonsf)
###############################################################################################################################
# chargement des bases et mise en forme
###############################################################################################################################
# 1) chargement des bases
taxi <- read_csv("C:/Users/dcaby/Desktop/projet_ensae/data/train.csv")
nyc <- geojson_sf("C:/Users/dcaby/Desktop/projet_ensae/data/nyu-2451-34509-geojson.json")
plot(nyc)
# 2) Traitements sur la table de base
taxi_c <- taxi %>%
  mutate(pickup_datetime = ymd_hms(pickup_datetime),
         dropoff_datetime = ymd_hms(dropoff_datetime),
         year = year(pickup_datetime),
         month = month(pickup_datetime),
         day = day(pickup_datetime),
         week_day = wday(pickup_datetime),
         pickup_hour = hour(pickup_datetime),
         pickup_minute = minute(pickup_datetime),
         pickup_second = hour(pickup_datetime),
         dropoff_hour = hour(dropoff_datetime),
         dropoff_minute = minute(dropoff_datetime),
         dropoff_second = hour(dropoff_datetime),
         # lubridate::hms est un parser, il ne récupère pas hour:minute:second dans un objet date
         # par contre, tu peux le reconstruire assez aisément
         # proposition ci-dessous
         pickup_time = hms(paste(pickup_hour, pickup_minute, pickup_second, sep = ":")),
         dropoff_time = hms(paste(dropoff_hour, dropoff_minute, dropoff_second, sep = ":"))) %>%
  select(-pickup_hour, -pickup_minute, -pickup_second, -dropoff_hour, -dropoff_minute, -dropoff_second)
# 3) On crée des cartes de pickup et des cartes de dropoff
# Attention au sens dans lequel on définit longitude (x) et latitude (y)
taxi_pickup <- st_as_sf(taxi,
                        coords = c("pickup_longitude","pickup_latitude"),
                        agr = "constant",
                        dim = "XY",
                        crs=4326, # = WGS84
                        stringsAsFactors = F,
                        remove = T)
taxi_dropoff <- st_as_sf(taxi,
                         coords = c("dropoff_latitude","dropoff_longitude"),
                         agr = "constant",
                         dim = "XY",
                         crs=4326, # = WGS84
                         stringsAsFactors = F,
                         remove = T)
# 4) Jointure avec nyc. L'idée étant de joindre sur le fait que l'on trouve le zip code
# Avec un inner_join on retire ceux dont les pickup et les dropoff se trouve hors nyc (j'imagine par exemple qu'on
# doit en avoir pour Newark)
taxi_pickup_zcta <- st_join(taxi_pickup, nyc, join = st_within) # possible d'ajouter left = F pour avoir un inner join
taxi_dropoff_zcta <- st_join(taxi_pickup, nyc, join = st_within)