---
title: "Statistical Analysis"
date: 2020-02-20
tags: [data wrangling, data science, messy data]
header:
  image: "/images/cityglobe.jpg"
excerpt: "Data Wrangling, Data Science, Messy Data"
mathjax: "true"
---

# Pacific Giant Salamander

<img src="{{ site.url }}{{ site.baseurl }}/images/sal_map.png" alt="linearly separable data">

# **Data & Methods**
Pacific Giant Salamander population size and demographics have been examined in Mack Creek, Oregon since 1993, as part of a larger [aquatic vertebrate population study](https://portal.edirepository.org/nis/mapbrowse?scope=knb-lter-and&identifier=4027&revision=12). [1] Areas of focus (reaches) include a section of clear cut forest (CC) and a 500 year-old conifer old growth forest (OG), located upstream of the CC forest. [1] Each reach is approximately 150 m in length, and blocked every 50 m by nets. [1] Samples are taken every 50 m along each reach using 2-pass electrofishing; length and weight data is recorded for all captured aquatic vertebrates. [1] The total number of Pacific Giant Salamanders counted at Mack Creek was visualized to compare abundance in CC forest to that in OG forest. Included is a table displaying Pacific Giant Salamander counts and proportions by channel classification for both OG and CC forests in the year 2017. Based on these values, a Chi-Square test for independence was used to determine if forest condition has a significant effect on the location within the channel at which Pacific Giant Salamanders are found. Additionally, to determine if there is a significant difference in mean weights between the two forest sections, mean Pacific Giant Salamander weights were compared across forest sections using a Welch’s two-sample t-test (alpha = 0.05). Beeswarm plots and quantile-quantile plots were then used to visualize mean weight distributions across each forest section, revealing an abnormal distribution of means. Knowing this, it would have been more accurate to visualize this data using Box-and-whisker plots, and to perform non-parametric statistical analyses to examine sample medians. This report presents analysis and figures exclusively prepared using .R software version 3.6.1. 

# **Analysis**
Annual counts of Pacific Giant Salamander were visually compared in OG and CC sections of Mack Creek (1993-2016). Visualizations display the changes in annual counts for each section of Mack Creek over time. From 1993 to approximately 2014, Pacific Giant Salamander counts in OG forest sections of Mack Creek generally exceed counts obtained in CC forest sections. From 2014 to 2016, this trend is reversed. General population growth and decay patterns are similar between both sites, displaying an overall increase in population from 1995 to approximately 2002. This is followed by an overall decline until 2006, a more drastic decline around 2014, and growth from 2014 to 2016. 

<img src="{{ site.url }}{{ site.baseurl }}/images/total_sal.png" alt="linearly separable data">

Here is the code to replicate the graph above:
```{r echo=FALSE, include=TRUE, warning=FALSE, out.width = "100%", fig.cap="***Figure 3.** Comparison of annual Pacific Giant Salamander counts in OG and CC sections of Mack Creek (1993-2016).*"}

# Results A: Visually compare annual salamander counts in old growth (OG) and clear cut (CC) sections of Mack Creek. For all years when Pacific giant salamander observations were recorded in the study, find the total annual Pacific giant salamander counts for the two sections of Mack Creek. Create a finalized graph to show the changes in annual counts for each section over time, add a figure caption, and briefly describe the trends in text. 

sal_abundance <- mack_date %>%
    dplyr::select(year, section, species) %>%
  group_by(year, section, species) %>%
  tally()

sal_total <- sal_abundance %>%
  group_by(section, species, year) %>%
  summarise(
    total = sum(n)
  )

sal_total_plot <- ggplot(sal_total, aes(x=year, y=total)) +
    geom_line(aes(color=section), size=1.2 ) +
    theme_light() +
    theme(
      legend.position="right") +
    labs(title = "Total Salamanders Counted at Mack Creek") +
   scale_color_viridis(discrete=TRUE, option="cividis") +
  labs(x = "year",
       y = "salamander count")

sal_total_plot
```

Below is a table of 2017 salamander counts by channel classification (pool, cascades and side-channel) in old growth (OG) and clear cut (CC) sections of Mack Creek.

<img src="{{ site.url }}{{ site.baseurl }}/images/sal_table.png" alt="linearly separable data">

Here is the code to replicate the graph above:
```{r echo=FALSE, include=FALSE, warning=FALSE}
# Results B: Table of 2017 salamander counts by channel classification (pool, cascades and side-channel) in old growth (OG) and clear cut (CC) sections of Mack Creek. Using only Pacific giant salamander observations from 2017, create a finalized table showing the counts and proportions of salamanders observed in different channel classifications (pool, cascade, or side-channel) within Mack Creek for the two sections (OG and CC). Add a table caption above the table. Note: We’re only interested in Pacific giant salamanders observed in the creek, so you should exclude salamanders observed in isolated pools (IP) disconnected from the channel. 

sal_summary <- mack_date %>%
  dplyr::select(unittype, section, year, species) %>%
  filter(unittype == "P" | unittype == "C" | unittype == "SC") %>%
  filter(year == 2017) %>%
  dplyr::select(-year) %>%
  group_by(unittype, section) %>%
  tally()


kable(sal_summary, col.names = c("Channel Classification",
                           "Section",
                           "Total Count"),
      caption = "***Figure 2.** Table of 2017 salamander counts by channel classification (pool, cascades and side-channel) in old growth (OG) and clear cut (CC) sections of Mack Creek*") %>% 
  kable_styling(bootstrap_options = c("striped", "hover")) %>% 
  kable_styling(bootstrap_options = "striped") %>%
  add_header_above(c("Pacific Salamander Counts: 2017" = 3))
```
The table below displays Pacific Giant Salamander counts and proportions by channel classification [pool (P), cascades (C), or side-channel (SC)] in 2017.

<img src="{{ site.url }}{{ site.baseurl }}/images/sal_table2.png" alt="linearly separable data">

Here is the code to replicate the table above:
```{r echo=FALSE, include=TRUE, warning=FALSE}

sal_adorn <- sal_count_table %>% 
  janitor::adorn_percentages(denominator = "row") %>% 
  adorn_pct_formatting(digits = 0) %>% 
  adorn_ns(position = "front")

kable(sal_adorn, caption = "***Figure 4.** The table below displays Pacific Giant Salamander counts and proportions by channel classification [pool (P), cascades (C), or side-channel (SC)] in 2017.*") %>% 
  kable_styling(bootstrap_options = c("striped", "hover")) %>% 
  kable_styling(bootstrap_options = "striped") %>%
  add_header_above(c("Pacific Salamander Counts and Proportions: 2017" = 4))
```
2017 Pacific Giant Salamander counts from the table above were used to determine if there is a significant effect of forest condition on where salamanders are found within the channel based on the channel classifications P, C, and SC. A Chi-Square test for independence revealed that there is *not* a significant association between forest condition and the number of salamanders counted within specific channel classifications $$($\chi$^2^(`r my_channel_chi$parameter`)) = `r my_channel_chi$statistic`$$ and $$($p-value$(`r my_channel_chi$parameter`)) = `r my_channel_chi$p.value`$$.

Here is the code to replicate the chi-square test above:
```r
chi_counts <- sal_count_table %>% 
  dplyr::select(-section)

chi_counts


# Run a chi-square test for independence using `chisq.test()`:

my_channel_chi <- chisq.test(chi_counts)

my_channel_chi

summary(my_channel_chi)
```
<img src="{{ site.url }}{{ site.baseurl }}/images/sal_stats.png" alt="linearly separable data">

A Welch two-sample t-test was used to determine if there is a significant difference in Pacific Giant Salamander mean weights between individuals found in CC or OG forest sections in 2017. Analysis shows that there is *no* significant difference in mean weight of Pacific Giant Salamanders (t(683.21) = 1.4859, p = 0.1378) located in CC forests (7.77587 ± 9.9 g [mean ± sd],  n = 368) and OG forests (6.701818 ± 9.035g [mean ± sd], n = 320).

Here is the code to run the t-test and built out stats table:

```{r, include=FALSE, warning=FALSE}
# Results D. Compare weights of Pacific giant salamanders in clear cut and old growth forest sections of the creek in 2017. Only considering creek section (OG or CC) as a variable (not further factoring by channel classification), answer: Is there a significant difference in mean weights for Pacific giant salamanders observed in the two forest sections (clear cut and old growth) in 2017? 

#Welch two sample ttest for comparing difference in means of clear cut and old growth sections.


#**Null hypothesis**: There is no significant difference in weight mean between old growth and clear cut sections (or, old growth and clear cut weight means not significantly different)

#**Alternative hypothesis**: There IS a significant difference in weight mean between old growth and clear cut (or, old growth and clear cut are significantly differenct)


sal_stats <- mack_date %>% 
  filter(year == 2017) %>%
  group_by(section) %>% 
  summarize(
    mean_weight = mean(weight, na.rm = TRUE),
    sd_weight = sd(weight, na.rm = TRUE),
    sample_size = n(),
    se_weight = sd(weight, na.rm = TRUE) / sqrt(n()),
    var_weight = var(weight, na.rm = TRUE)
  )

sal_stats

kable(sal_stats, col.names = c("Section",
                           "Mean Weight",
                           "SD Weight", "Sample Size",
                           "SE Weight",
                           "Var Weight"),
        caption = 
            "Figure 5. The table below includes the means, standard deviations, and sample weights of Pacific Giant Salamanders foung in CC or OG forest sections in 2017.") %>% 
  kable_styling(bootstrap_options = c("striped", "hover")) %>%
  add_header_above(c("Pacific Salamander Statistics: 2017" = 6))


# lRun a two-sample T-Test to see if there is a significant difference in the means of clear cut vs. old growth sections. 

# Question: Are clear cut salamander significantly different in weight than old growth slamanders(using a significance level of 0.05)?

sec_welch <- mack_date %>%
  dplyr::select(section, year, weight, unittype) %>%
  filter(unittype == "P" | unittype == "C" | unittype == "SC") %>%
  filter(year == 2017) %>%
  dplyr::select(-year) %>%
  group_by(weight, section)

cc_sample <- sec_welch %>%
  filter(section == "CC") %>%
  pull(weight)

t.test(cc_sample)

og_sample <- sec_welch %>%
  filter(section == "OG") %>%
  pull(weight)

t.test(og_sample)

section_ttest <- t.test(cc_sample,og_sample)

section_ttest

# A resulting p-value under 0.05 means that variances are not equal and than further parametric tests such as ANOVA are not suited. We should use chi-squared.
```

<img src="{{ site.url }}{{ site.baseurl }}/images/weight_sal.png" alt="linearly separable data">

The graph below visualizes Pacific Giant Salamander weights for individuals found in CC and OG forests. The bar heights represent mean Salamander weight for each section. The red error bar indicates how individual observations are dispersed around the mean. It appears that measurements from the OG forest section are slightly more precise than those from the CC forest section.  

<img src="{{ site.url }}{{ site.baseurl }}/images/fire_sal.png" alt="linearly separable data">

Here is the code to run your own jitter plot with statistics bar:
```{r echo=FALSE, include=TRUE, warning=FALSE, out.width = "100%", fig.cap="***Figure 5.** Observed Pacific Giant Salamander weights in relation to mean Pacific Giant Salamander weights in OG and CC forests sections.*"}
# Results C continued.

sal_weight <- mack_date %>%
  dplyr::select(year, section, weight) %>%
  filter(year == 2017)

sal_weight_plot <- ggplot() +
  geom_beeswarm(data = sal_weight, 
                aes(x = section, 
                    y = weight),
                size = 3,
                alpha = 0.6,
                pch = 16,
                color = "gray70") +
  labs(title = "Mean Weights of Pacific Giant Salamanders (CC & OG Forests)") +
  scale_x_discrete(labels = c("Clear Cut", "Old Growth")) +
  geom_point(data = sal_stats, aes(x = section, y = mean_weight), 
             color = "firebrick",
             size = 4) +
  geom_errorbar(data = sal_stats, 
                aes(x = section, 
                    ymin = mean_weight - sd_weight,
                    ymax = mean_weight + sd_weight),
                color = "firebrick",
                width = 0.1) +
  theme_light()

sal_weight_plot

```

We need to see if the weight in Salamanders is normally distrubted between the two sections. To do this. We will use a qq plot.  Based on the results of our qq plot below, the data is NOT normally distributed.

<img src="{{ site.url }}{{ site.baseurl }}/images/dist_sal.png" alt="linearly separable data">

Here is the code to run a qq-plot to analyze distribution:
```{r echo=FALSE, include=TRUE, warning=FALSE, out.width = "100%", fig.cap="***Figure 6.** This QQ-Plot visualizes the skewed distribution in Pacific Giant Salamander weights in both OG and CC forest sections.*"}
# We need to see if the weight in Salamanders is normally distrubted between the two sections. To do this. We will use a qq plot.

ggplot(data = mack_date, aes(sample = weight)) +
  geom_qq(aes(color = section),
                 alpha = 0.5,
                 show.legend = FALSE
          ) +
  labs(title = "Distribution of Pacific Giant Salamander Weights (OG and CC forests)") +
  facet_wrap(~section, scales = "free") +
  scale_color_viridis(discrete = TRUE, option = "cividis")

# Based on the results of our qq plot, the data is NOT normally distributed.
```


