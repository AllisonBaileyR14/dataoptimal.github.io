---
title: "Environmental Survey Project"
date: 2020-02-20
tags: [data wrangling, data science, messy data]
header:
  image: "/images/us_map.jpg"
excerpt: "Data Wrangling, Data Science, Messy Data"
mathjax: "true"
---

## Environmental Survey Analysis

<img src="{{ site.url }}{{ site.baseurl }}/images/sal_map.png">

In 2017, UCSB-Bren PhD candidate, Alexander DeGolia, published his dissertation "Choosing Green: Explaining Motivations Across Different Environmental Behaviors".[1] Part of the research included an extensive survey to California residence to evaluate motivations that drive support or opposition towards environmental policies and programs. The data collected on each residence was broken down into age, gender, education level, income level, and political affiliation.  Each resident was asked their level of support (Strongly Oppose, Mostly Oppose, Neutral, Mostly Support, Strongly Support) for three California environmental programs: Cap and Trade, Land Conservation, and Renewable Energy. 

A Principal component analysis (PCA) was conducted to evaluate the variables in the survey that were most correlated with the levels of support for the Land Conservation Program.


<img src="{{ site.url }}{{ site.baseurl }}/images/total_sal.png">

Here is the code to replicate the graph above:
```r
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

<img src="{{ site.url }}{{ site.baseurl }}/images/sal_table.png">

Here is the code to replicate the graph above:
```r 
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

<img src="{{ site.url }}{{ site.baseurl }}/images/sal_table2.png">

Here is the code to replicate the table above:
```r

sal_adorn <- sal_count_table %>% 
  janitor::adorn_percentages(denominator = "row") %>% 
  adorn_pct_formatting(digits = 0) %>% 
  adorn_ns(position = "front")

kable(sal_adorn, caption = "***Figure 4.** The table below displays Pacific Giant Salamander counts and proportions by channel classification [pool (P), cascades (C), or side-channel (SC)] in 2017.*") %>% 
  kable_styling(bootstrap_options = c("striped", "hover")) %>% 
  kable_styling(bootstrap_options = "striped") %>%
  add_header_above(c("Pacific Salamander Counts and Proportions: 2017" = 4))
```
2017 Pacific Giant Salamander counts from the table above were used to determine if there is a significant effect of forest condition on where salamanders are found within the channel based on the channel classifications P, C, and SC. A Chi-Square test for independence revealed that there is *not* a significant association between forest condition and the number of salamanders counted within specific channel classifications ($\chi$^2^(`r my_channel_chi$parameter`)) = `r my_channel_chi$statistic` and ($p-value$(`r my_channel_chi$parameter`)) = `r my_channel_chi$p.value`.

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
<img src="{{ site.url }}{{ site.baseurl }}/images/sal_stats.png">

A Welch two-sample t-test was used to determine if there is a significant difference in Pacific Giant Salamander mean weights between individuals found in CC or OG forest sections in 2017. Analysis shows that there is *no* significant difference in mean weight of Pacific Giant Salamanders (t(683.21) = 1.4859, p = 0.1378) located in CC forests (7.77587 ± 9.9 g [mean ± sd],  n = 368) and OG forests (6.701818 ± 9.035g [mean ± sd], n = 320).

Here is the code to run the t-test and built out stats table:

```r
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

<img src="{{ site.url }}{{ site.baseurl }}/images/weight_sal.png">

The graph below visualizes Pacific Giant Salamander weights for individuals found in CC and OG forests. The bar heights represent mean Salamander weight for each section. The red error bar indicates how individual observations are dispersed around the mean. It appears that measurements from the OG forest section are slightly more precise than those from the CC forest section.  

<img src="{{ site.url }}{{ site.baseurl }}/images/fire_sal.png">

Here is the code to run your own jitter plot with statistics bar:
```r
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

<img src="{{ site.url }}{{ site.baseurl }}/images/dist_sal.png">

Here is the code to run a qq-plot to analyze distribution:
```r
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
Now we will visually compare Pacific giant salamander weights between the three channel classifications. 

<img src="{{ site.url }}{{ site.baseurl }}/images/fire2_sal.png">

Here is the code to run the graph above:
```r

channel_weight_plot <- ggplot() +
  geom_beeswarm(data = weight_channel, 
                aes(x = unittype, 
                    y = weight,
                    ),
                size = 3,
                alpha = 0.6,
                pch = 16,
                color = "gray70") +
  scale_x_discrete(labels = c("C", "P", "SC")) +
  geom_point(data = weight_summary, aes(x = unittype, y = mean_weight), 
             color = "firebrick",
             size = 4) +
  geom_errorbar(data = weight_summary, 
                aes(x = unittype, 
                    ymin = mean_weight - sd_weight,
                    ymax = mean_weight + sd_weight),
                color = "firebrick",
                width = 0.1) +
  labs(title = "Mean Pacific Giant Salamander Weights (P, C, and SC Channels)",
       x = "channel") +
  theme_light()

channel_weight_plot

```
Mean Pacific Giant Salamander weights did *not* differ significantly between Pool (9.29 +/- 1.56) and Cascades (7.52 +/- 0.43) nor did it differ significantly between the Cascades and the Side Channel (5.68 +/- 0.65).  However, there *is* a significant difference between Salamander weights when comparing individuals found in the Side Channel to those found in Pool channel classifications (one-way ANOVA with post-hoc Tukey's HSD, F(2) = 4.2156, SC-P *p adj* < 0.05). However, after running the difference against a Cohen's d test and receiving a value of approximately 0.352 it can be concluded that the difference in mean Salamander weight between Pool and Side Channel classifications is small.

Here is a [link](https://github.com/AllisonBaileyR14/Pacific-Salamanders)to the repository to fork and clone for yourself! 

#### **References**

[1]Andrews Forest LTER Site and S. V. Gregory, “Aquatic Vertebrate Population Study in Mack Creek, Andrews Experimental Forest, 1987 to present.” Environmental Data Initiative, 2016.

[2]“Pacific Giant Salamander | Oregon Wild.” [Online]. Available: https://oregonwild.org/wildlife/pacific-giant-salamander. [Accessed: 30-Nov-2019].

[3]“The IUCN Red List of Threatened Species,” IUCN Red List of Threatened Species. [Online]. Available: https://www.iucnredlist.org/en. [Accessed: 01-Dec-2019].

