---
title: "Mapping Projects"
date: 2020-02-20
tags: [data wrangling, data science, messy data]
header:
  image: "/images/cityglobe.jpg"
excerpt: "Data Wrangling, Data Science, Messy Data"
mathjax: "true"
---

# Pacific Giant Salamander

Here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/sal_map.png" alt="linearly separable data">

# **Data & Methods**
Pacific Giant Salamander population size and demographics have been examined in Mack Creek, Oregon since 1993, as part of a larger [aquatic vertebrate population study](https://portal.edirepository.org/nis/mapbrowse?scope=knb-lter-and&identifier=4027&revision=12). [1] Areas of focus (reaches) include a section of clear cut forest (CC) and a 500 year-old conifer old growth forest (OG), located upstream of the CC forest. [1] Each reach is approximately 150 m in length, and blocked every 50 m by nets. [1] Samples are taken every 50 m along each reach using 2-pass electrofishing; length and weight data is recorded for all captured aquatic vertebrates. [1] The total number of Pacific Giant Salamanders counted at Mack Creek was visualized to compare abundance in CC forest to that in OG forest. Included is a table displaying Pacific Giant Salamander counts and proportions by channel classification for both OG and CC forests in the year 2017. Based on these values, a Chi-Square test for independence was used to determine if forest condition has a significant effect on the location within the channel at which Pacific Giant Salamanders are found. Additionally, to determine if there is a significant difference in mean weights between the two forest sections, mean Pacific Giant Salamander weights were compared across forest sections using a Welch’s two-sample t-test (alpha = 0.05). Beeswarm plots and quantile-quantile plots were then used to visualize mean weight distributions across each forest section, revealing an abnormal distribution of means. Knowing this, it would have been more accurate to visualize this data using Box-and-whisker plots, and to perform non-parametric statistical analyses to examine sample medians. This report presents analysis and figures exclusively prepared using .R software version 3.6.1. 

# **Analysis**
Annual counts of Pacific Giant Salamander were visually compared in OG and CC sections of Mack Creek (1993-2016). Visualizations display the changes in annual counts for each section of Mack Creek over time. From 1993 to approximately 2014, Pacific Giant Salamander counts in OG forest sections of Mack Creek generally exceed counts obtained in CC forest sections. From 2014 to 2016, this trend is reversed. General population growth and decay patterns are similar between both sites, displaying an overall increase in population from 1995 to approximately 2002. This is followed by an overall decline until 2006, a more drastic decline around 2014, and growth from 2014 to 2016. 
 

### H3 Heading

Here's some basic text.

And here's some *italics*

Here's some **bold** text.

What about a [link](https://github.com/dataoptimal)?

Here's a bulleted list:
* First item
+ Second item
- Third item

Here's a numbered list:
1. First
2. Second
3. Third

Python code block:
```python
    import numpy as np

    def test_function(x, y):
      z = np.sum(x,y)
      return z
```

R code block:
```r
library(tidyverse)
df <- read_csv("some_file.csv")
head(df)
```

Here's some inline code `x+y`.

Here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/hawaii_static.png">


<img src="{{ file:///Users/allisonbailey/Desktop/Winter%202020%20Courses/Adv%20Data/Homework%202/hawaii_bailey/hawaii_map_ag.html }}{{ file:///Users/allisonbailey/Desktop/Winter%202020%20Courses/Adv%20Data/Homework%202/hawaii_bailey/hawaii_map_ag.html}}">

Here's another image using Kramdown:
![alt]({{ site.url }}{{ site.baseurl }}/images/perceptron/linsep.jpg)

Here's some math:

$$z=x+y$$

You can also put it inline $$z=x+y$$

