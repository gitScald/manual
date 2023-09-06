# manual

This is the OBIS manual which is published at https://manual.obis.org/.

## Contribute

The contribute to the manual, please follow these steps:

- Fork the repository to your own GitHub account by clicking the `Fork` button (top right of this page).
- Edit your version of the manual.
- You should now see a notification saying `This branch is 1 commit ahead of iobis:master.`. Click the `Pull request` button.
- Review your changes and click `Create pull request`.
- Enter a title and description for your pull request. If your pull request addresses specific GitHiub issues, mention them in the description.
- Click `Create pull request`.

<ul>
  <li>Boop</li>
  <li><details>
    <summary>How to obtain a taxon checklist for an area?</summary>
    <br>
    
    There are a few possible ways to obtain a taxon checklist for a given area. We will obtain a checklist of species in the Albain EEZ as an example. To do this we will create a bounding box around our area of interest, and then apply filters to simplify the geometry.
    ```r
    library(mregions)
    library(dplyr)
    library(robis)
    library(sf)
    #obtain Albanian EEZ as sf
    geom <- mr_shp(key = "MarineRegions:eez", filter = "Albanian Exclusive Economic Zone", maxFeatures = NULL)
    #get WKT for the bounding box
    wkt <- st_as_text(st_as_sfc(st_bbox(geom)), digits = 6)
    #fetch occurrences for bounding box
    occ <- occurrence(geometry = wkt) %>%
      st_as_sf(coords = c("decimalLongitude", "decimalLatitude"), crs = 4326)
    #filter using geometry
    occ_filtered <- occ %>%
      filter(st_intersects(geometry, geom, sparse = FALSE)) %>%
      as_tibble() %>%
      select(-geometry)
    #get taxa
    alb_taxa <- occ_filtered %>%
      group_by(phylum, class, order, family, genus, species, scientificName) %>%
      summarize(records = n())
    ```
  </details></li>
</ul>
