### Combining sac objects into one dataframe 
This part of the code combines all your Species Accumulation Curve (sac) objects into one data frame. For each sac object, we create a data frame that includes the latitude bin, the sites, and the richness. The latitude bin is simply the index of the sac object in the list.

```
df_all_sac <- do.call(rbind, lapply(seq_along(list_sac), function(i) {
  data.frame(Latitude_Bin = latitude_bins[i], 
             Sites = list_sac[[i]]$sites, 
             Richness = list_sac[[i]]$richness)
}))
```
### Setting up a color palette
We're using the RColorBrewer package to set up a color-blind-friendly palette with 9 colors. This will be used later for visualizing our data.
```
library(RColorBrewer)
my_colors <- brewer.pal(9, "Set1")
```

### Calculating median richness per Latitude_Bin
Here, we calculate the median richness for each Latitude_Bin, both for storing these values in a separate dataframe medians, and adding this new calculated field median_richness to our main dataframe df_all_sac.

```
medians <- df_all_sac %>%
  group_by(Latitude_Bin) %>%
  summarise(median_richness = median(Richness))

df_all_sac <- df_all_sac %>%
  group_by(Latitude_Bin) %>%
  mutate(median_richness = median(Richness))
```
### Preparing data for plotting
We create a copy of our dataframe for plotting purposes. The Latitude_Bin is then reordered in df_all_sac based on the median richness calculated previously.
```
df_all_sac_order <- df_all_sac

df_all_sac <- df_all_sac %>%
  ungroup() %>%
  mutate(Latitude_Bin = reorder(Latitude_Bin, -median_richness))
```

### Plotting the data
We create two plots. The first plot uses df_all_sac in which the data is ordered by the median richness. The second plot uses df_all_sac_order to display the data in its original order of latitude.

```
ggplot(df_all_sac, aes(x = log10(Sites), y = Richness, color = Latitude_Bin)) +
  geom_line(size = 1.2, alpha = 0.6) +  
  scale_color_manual(values = my_colors) +
  labs(title = "Species Accumulation Curves: From Lower to Higher Richness",
       x = "Log(Observations)",
       y = "Species Richness",
       color = "Latitude Bin") +
  theme_minimal()

ggplot(df_all_sac_order, aes(x = log10(Sites), y = Richness, color = Latitude_Bin)) +
  geom_line(size = 1.2, alpha = 0.6) +  
  scale_color_manual(values = my_colors) +
  labs(title = "Species Accumulation Curves: Latitude Order",
       x = "Log(Observations)",
       y = "Species Richness",
       color = "Latitude Bin") +
  theme_minimal()
```

### Saving the workspace
```
save.image("/Users/andreahernandez
```