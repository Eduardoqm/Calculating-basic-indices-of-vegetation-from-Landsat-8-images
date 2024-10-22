### **Calculating basic indices of vegetation from Landsat-8 images**

Vegetation rates are important for anyone who wants to know about the conditions of vegetation, whether from a forest that you want to know how much is preserved, or a crop or pasture that you want to know about your health. The site used in the example is a small park in Brazil.

```
library(raster)
library(rasterVis)
library(rgdal)
```

We need to open our image in the R environment, but each Landsat scene comes with multiple bands. We will create a list of bands and then we'll make a stack. But to facilitate understanding we will carry the bands of our interest in diferentes objects.

```
list <- list.files(path="C:/Users/Documents/Bacaba", pattern = ".tif$", full.names=TRUE,recursive=TRUE)

bands <- brick(stack(list))
levelplot(bands[[9]])

```

![Cena](https://user-images.githubusercontent.com/24628679/61597081-0d8fd780-abe2-11e9-9f5c-9edf8c523b79.jpg)

But before separating the bands we will cut them with the limits of the park

`bcb=readOGR(dsn = "C:/Users/Documents/shapes",layer="limite_reserva_total")`

Change projection so data is compatible
```
proj=projection(bands[[1]])
bcb_sr=spTransform(bcb,proj)
```

Cutting the boundaries
`bands=mask(bands,bcb_sr)`

Now it's time to separate the bands we'll use. Here we will need band 2 up to band 7:
```
band2 <- bands[[4]]
band3 <- bands[[5]]
band4 <- bands[[6]]
band5 <- bands[[7]]
band6 <- bands[[8]]
band7 <- bands[[9]]
```

Now it's time to calculate the indices. Let us calculate the indices of NDVI, EVI2 and NDMI.

### **Normalized Difference Vegetation Index (NDVI)**
This index is a numerical indicator that uses the near red and infrared spectral bands. NDVI is highly associated with vegetation content. High NDVI values correspond to areas that reflect more on the near-infrared spectrum. The higher reflectance in the near infrared corresponds to a denser and healthier vegetation. The NDVI is calculated by following this equation:

![NDVI_Traditional_Formula](https://user-images.githubusercontent.com/24628679/61597106-6d867e00-abe2-11e9-9592-49c173d23369.jpg)


Using the corresponding bands:
`ndvi<- (band5-band4)/(band5+band4)`


![NDVI](https://user-images.githubusercontent.com/24628679/61597125-a6265780-abe2-11e9-9ca2-906b8a166b65.jpg)


### **Enhanced vegetation index - Two-band (EVI2)**
This index is an 'optimized' vegetation index designed to improve the vegetation signal with better sensitivity in regions of high biomass and better monitoring of vegetation through a decoupling of the canopy bottom signal and a reduction in atmospheric influences. The EVI is calculated by following this equation:

![EVI2](https://user-images.githubusercontent.com/24628679/61597170-69a72b80-abe3-11e9-8602-72e284dbc959.png)


Using the corresponding bands:
`evi2 <- 2.5*((band5-band4)/(band5+2.4*band4+1))`


![EVI2](https://user-images.githubusercontent.com/24628679/61597139-e4bc1200-abe2-11e9-9cb7-539cfd2167cf.jpg)


### **Normalized Difference Moisture Index (NDMI)**
This index is a numerical indicator that is used in combination with other vegetation indexes (NDVI and / or AVI), which is associated with vegetation humidity. NDMI uses short-wave and short-wave spectral bands to capture moisture variations in vegetated areas. Monitoring of droughts and subtle changes in vegetation moisture conditions. NDMI can also be used to determine fuel moisture for forest fire risk assessments. The NDMI is calculated by following this equation:

![tUL7izyqRiqYCb1HpEpW_Screen Shot 2017-10-16 at 01 05 02](https://user-images.githubusercontent.com/24628679/61629542-9dbe3300-ac5b-11e9-9346-c2bb75d3b93e.png)


Using the corresponding bands:
`ndmi <- (band5-band6)/(band5+band6)`

![NDMI](https://user-images.githubusercontent.com/24628679/61597146-fdc4c300-abe2-11e9-82b6-a3e67d144feb.jpg)


Now let's save our results!

```
writeRaster(ndvi, filename = "NDVI.tif", format="GTiff")

writeRaster(evi2, filename = "EVI2.tif", format="GTiff")

writeRaster(ndmi, filename = "NDMI.tif", format="GTiff")
```

This was just a small example of what can be done with the Landsat data in conjunction with the R environment. It is possible to do much more with the possibilities of using different formulas and bands. Not only of Landsat, but of several other satellites!
