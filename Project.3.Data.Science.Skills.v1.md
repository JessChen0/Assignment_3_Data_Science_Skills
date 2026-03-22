# Project.3.Data.Science.Skills
Ciara Bonnet, Jess Chen, Muhammad Ahmad, Nana Kwasi Danquah, Theresa
Benny

# Project 3 \| Data Science \| Most Valued Skills

# Team Members:

Ciara Bonnet, Jess Chen, Muhammad Ahmad, Nana Kwasi Danquah, Theresa
Benny

# Introduction

To assess “the most valued skills in data science”, our team recognized
it is important to first define value, as this could lead to various
interpretations. Ultimately, we wanted to take the perspective of Data
Science students (as we are!) and job seekers to define value as most
sought after or highest paid skills.

With this narrower definition, we chose the “[Global AI & Data Science
Job Market
(2020-2026)](https://www.kaggle.com/datasets/mann14/global-ai-and-data-science-job-market-20202026?select=country_ai_trends.csv)”
from Kaggle. This dataset is sourced from LinkedIn which is a well
recognized global source for job postings and we liked that it contains
data from several years, salary information and AI roles.

We investigated which data science skills are most valued using two main
files from our dataset: **ai_jobs.csv** and **skills_demand.csv**. The
ai_jobs.csv file contains job-level information such as title, industry,
location, experience level, and salary range. The skills_demand.csv file
contains skill information linked to each job by job_id. We loaded both
files into R, cleaned the data, checked for missing values and
duplicates, and joined the tables using job_id as the primary key. (see
ER Diagram below for details)

As a team, we will utilize our slack channel as our primary form of
communication and a team github repo to store working files. We will
update file names with versions numbers to manage version control.

## ER Diagram Potential Data sets:

![](https://raw.githubusercontent.com/JessChen0/Assignment_3_Data_Science_Skills/main/images/ER_Diagram.png)

# Data Loading/Cleaning and Transformation

We posted the raw data tables into github and loaded them below.
Checking for errors and large numbers.

``` r
library(dplyr)
```

    Warning: package 'dplyr' was built under R version 4.4.3


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(ggplot2)
```

    Warning: package 'ggplot2' was built under R version 4.4.3

``` r
library(naniar)
```

    Warning: package 'naniar' was built under R version 4.4.3

``` r
library(tidyr)
```

    Warning: package 'tidyr' was built under R version 4.4.3

``` r
library(knitr)
```

    Warning: package 'knitr' was built under R version 4.4.3

``` r
data_jobs_raw <- read.csv("https://raw.githubusercontent.com/JessChen0/Assignment_3_Data_Science_Skills/refs/heads/main/ai_jobs.csv")

skills_raw <- read.csv("https://raw.githubusercontent.com/JessChen0/Assignment_3_Data_Science_Skills/refs/heads/main/skills_demand.csv")
```

Based on the summary there are no big errors or weirdly small or large
numbers. \# Check for null or missing rows

``` r
vis_miss(data_jobs_raw)
```

![](Project.3.Data.Science.Skills.v1_files/figure-commonmark/unnamed-chunk-3-1.png)

``` r
vis_miss(skills_raw)
```

![](Project.3.Data.Science.Skills.v1_files/figure-commonmark/unnamed-chunk-3-2.png)

Heat maps to check for any missing values. There are no missing values
in this data set.

### Filter the data sets by country (we just want USA)

``` r
data_jobs_filtered <- data_jobs_raw %>% filter(country =="USA") 

data_jobs_USA <- data_jobs_filtered %>% select(-country)
glimpse(data_jobs_USA)
```

    Rows: 8,274
    Columns: 13
    $ job_id               <chr> "9T84AZYTJXEP", "SHNF877VREN9", "5O7UUBCXULJY", "…
    $ job_title            <chr> "Data Analyst", "AI Researcher", "AI Researcher",…
    $ company_type         <chr> "Startup", "Startup", "Startup", "Research Lab", …
    $ industry             <chr> "Healthcare", "Tech", "Healthcare", "Finance", "H…
    $ city                 <chr> "Seattle", "Austin", "New York", "Remote", "Seatt…
    $ remote_type          <chr> "Hybrid", "Onsite", "Onsite", "Remote", "Onsite",…
    $ experience_level     <chr> "Senior", "Senior", "Senior", "Mid", "Entry", "Mi…
    $ min_experience_years <int> 5, 5, 5, 2, 0, 2, 5, 2, 2, 2, 0, 5, 2, 5, 0, 0, 5…
    $ salary_min_usd       <int> 148201, 143946, 146484, 97041, 60926, 103770, 149…
    $ salary_max_usd       <int> 179967, 173591, 159288, 109176, 78382, 120315, 17…
    $ employment_type      <chr> "Full-time", "Full-time", "Full-time", "Full-time…
    $ posted_year          <int> 2021, 2020, 2025, 2020, 2022, 2021, 2025, 2026, 2…
    $ company_size         <chr> "Large", "Small", "Large", "Medium", "Medium", "L…

Filtered for “USA” then got rid of the column since it is not necessary
anymore

### Converting skills from long to wide

``` r
skills_wide <- skills_raw %>%
  pivot_wider(
    id_cols  = job_id,
    names_from = skill,
    values_from = skill_level
  )

skills_wide
```

    # A tibble: 50,000 × 12
       job_id   R     `Computer Vision` NLP   SQL   Python AWS   Azure GCP   PyTorch
       <chr>    <chr> <chr>             <chr> <chr> <chr>  <chr> <chr> <chr> <chr>  
     1 E0IFD0T… Adva… Basic             Inte… Inte… <NA>   <NA>  <NA>  <NA>  <NA>   
     2 ZMF8MDD… <NA>  <NA>              Adva… <NA>  Basic  Adva… Inte… <NA>  <NA>   
     3 CX1945N… <NA>  Basic             <NA>  <NA>  Advan… <NA>  <NA>  Basic Advanc…
     4 QJ7YHL1… Adva… <NA>              Adva… Basic <NA>   <NA>  Basic <NA>  <NA>   
     5 JIUJV6O… <NA>  <NA>              <NA>  <NA>  Inter… Inte… Basic <NA>  <NA>   
     6 9T84AZY… Inte… <NA>              Basic <NA>  <NA>   <NA>  <NA>  <NA>  <NA>   
     7 4SHNF87… <NA>  Advanced          Adva… Adva… <NA>   Inte… Basic <NA>  Interm…
     8 9TEX8FS… <NA>  Basic             Basic <NA>  <NA>   <NA>  <NA>  Inte… <NA>   
     9 ICATVAZ… <NA>  <NA>              Basic <NA>  Inter… <NA>  Adva… <NA>  Advanc…
    10 IRODXMO… <NA>  <NA>              Inte… Adva… <NA>   Adva… Adva… <NA>  <NA>   
    # ℹ 49,990 more rows
    # ℹ 2 more variables: `Scikit-learn` <chr>, TensorFlow <chr>

Here is the wide version of skills I thought this will be the best way
to go about it because now we can do numerical operations more
conveniently and without having to make a column for every skill level.

### Combining them into a new table

``` r
jobs_skills <- data_jobs_USA %>%
  select(job_id, job_title, city, salary_min_usd, salary_max_usd, posted_year) %>%
  left_join(
    skills_wide %>% select(job_id, R, `Computer Vision`, NLP, SQL, Python, AWS, Azure, GCP, PyTorch, `Scikit-learn`, TensorFlow),
    by = "job_id"
  ) %>% 
  mutate( avg_salary = (salary_min_usd + salary_max_usd) / 2) %>%
  select(-salary_min_usd, -salary_max_usd )
```

``` r
glimpse(jobs_skills)
```

    Rows: 8,274
    Columns: 16
    $ job_id            <chr> "9T84AZYTJXEP", "SHNF877VREN9", "5O7UUBCXULJY", "1WJ…
    $ job_title         <chr> "Data Analyst", "AI Researcher", "AI Researcher", "A…
    $ city              <chr> "Seattle", "Austin", "New York", "Remote", "Seattle"…
    $ posted_year       <int> 2021, 2020, 2025, 2020, 2022, 2021, 2025, 2026, 2022…
    $ R                 <chr> "Intermediate", NA, NA, NA, NA, "Advanced", NA, NA, …
    $ `Computer Vision` <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ NLP               <chr> "Basic", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
    $ SQL               <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ Python            <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ AWS               <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ Azure             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ GCP               <chr> NA, NA, NA, NA, NA, "Basic", NA, NA, NA, NA, NA, NA,…
    $ PyTorch           <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ `Scikit-learn`    <chr> "Intermediate", NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ TensorFlow        <chr> NA, NA, NA, NA, NA, "Basic", NA, NA, NA, NA, NA, NA,…
    $ avg_salary        <dbl> 164084.0, 158768.5, 152886.0, 103108.5, 69654.0, 112…

# Data Analysis

### Top 6 Job Titles in the U.S

{r} library(dplyr) library(gt) library(scales)

top_titles \<- jobs_skills %\>% count(job_title, name = “num_postings”)
%\>% arrange(desc(num_postings)) %\>% slice_head(n = 25) %\>%
mutate(rank = row_number()) %\>% select(rank, job_title, num_postings)
\# 👈 move column here instead

top_titles %\>% gt() %\>% cols_label( rank = “Rank”, job_title = “Job
Title”, num_postings = “Number of Postings” ) %\>% tab_header( title =
“Top 6 Job Titles in the U.S.”, subtitle = “Based on AI & Data Science
Job Listings” ) %\>% fmt_number( columns = num_postings, decimals = 0,
use_seps = TRUE ) %\>% data_color( columns = num_postings, colors =
col_numeric( palette = c(“lightblue”, “darkblue”), domain = NULL ) )
%\>% cols_align( align = “center”, columns = c(rank, num_postings) )
%\>% tab_options( table.font.size = px(13), heading.title.font.size =
px(18), heading.subtitle.font.size = px(13) )

### Most popular skills 2020 vs. 2025

The below compares the most “requested” skills, according to linkedIn
job postings in 2020 and 2025. SQL and TensorFlow skills were tied for
most valuable in 2020 but “Computer Vision” and Azure have now become
tied for most popular.

``` r
# define the columns to call out later
all_skills <- c("R","Computer Vision","NLP","SQL","Python","AWS","Azure","GCP","PyTorch","Scikit-learn","TensorFlow")

# define a function
plot_popular_skill <- function(jobs_skills,selected_year) {

summary_skills <- jobs_skills %>%
  filter(posted_year == selected_year) %>%
  summarise(across(all_of(all_skills), ~sum(!is.na(.)))) %>% #sums each skill column that isn't empty
  pivot_longer(cols = everything(), #turns to long for ggplot
               names_to = "column",
               values_to = "count") %>%
  mutate (
    total = sum(count),
    percentage = count / total * 100  #counts total number of mentions and calculates %
  ) %>%
  arrange(desc(count)) %>%
  mutate(column=factor(column,levels = column))  # lock cells in order

#count
Total_jobs <- jobs_skills %>% filter(posted_year == 2020) %>% nrow()
cat("Total jobs captured in 2020:", Total_jobs)
cat("\n")
cat("Total non-NA entries in 2020:", summary_skills$total[1])
cat("\n")
cat("\n")
Total_jobs <- jobs_skills %>% filter(posted_year == 2025) %>% nrow()
cat("Total jobs captured in 2025:", Total_jobs)
cat("\n")
cat("Total non-NA entries in 2025:", summary_skills$total[1])
#plot

ggplot(summary_skills, aes(x = column, y = count)) +
  geom_col(fill = "lightskyblue") +
  geom_text(
    aes(label = sprintf("%.1f%%", percentage)),
    hjust = -.5, size = 3.2
  ) +
  scale_x_discrete(guide = guide_axis(angle = 45)) +  # tilts labels
  scale_y_continuous(expand = expansion(mult = c(0, 0.15))) +  # headroom for labels
  coord_flip() +
  labs(
    title    = paste("Most Popular Skills", selected_year),
    subtitle = "Ranked by number of times a skill is requested in job post",
    x        = NULL,
    y        = "Number of job posts asking for skill"
  ) +
  theme_minimal(base_size = 13) +
  theme(
    plot.title    = element_text(face = "bold"),
    panel.grid.major.x = element_blank()  # removes grid lines
  )
}


#Run full functions for 2020 and 2025  
plot_popular_skill(jobs_skills,2020)
```

    Total jobs captured in 2020: 1188
    Total non-NA entries in 2020: 227

    Total jobs captured in 2025: 1179
    Total non-NA entries in 2025: 227

![](Project.3.Data.Science.Skills.v1_files/figure-commonmark/unnamed-chunk-8-1.png)

``` r
plot_popular_skill(jobs_skills,2025)
```

    Total jobs captured in 2020: 1188
    Total non-NA entries in 2020: 225

    Total jobs captured in 2025: 1179
    Total non-NA entries in 2025: 225

![](Project.3.Data.Science.Skills.v1_files/figure-commonmark/unnamed-chunk-8-2.png)

### Skills by city/location

We also wanted to look at which cities are attracting the most jobs
since 2025. Not surprisingly, “remote” is the most common hiring
location. Interestingly, the other top 4 cities are hiring at similar
paces.

``` r
jobs_skills %>%
  filter(posted_year %in% c(2025, 2026)) %>%
  count(city, name = "city_count") %>%
  arrange(desc(city_count)) %>%
  slice_head(n = 5) %>%
  kable(col.names = c("Location", "Number of Job Listings"), caption = "Top 5 Data Sci & AI Hiring Cities/Locations in 2025/26")
```

| Location      | Number of Job Listings |
|:--------------|-----------------------:|
| Remote        |                    772 |
| Austin        |                    355 |
| New York      |                    321 |
| San Francisco |                    307 |
| Boston        |                    306 |

Top 5 Data Sci & AI Hiring Cities/Locations in 2025/26

### Top skills associated with higher pay

As we also defined value as high paying jobs/skill sets, we also looked
at most popular skill sets associated with the top 25% highest paying
roles. We wrote the function to have flexibility in threshold so we can
see how these stats change for different percentiles (e.g., top 20%
highest paying roles, top 50%, etc.)

``` r
plot_highsalary_skills <- function(data) {
  
  # calculate the 75th percentile salary threshold
  salary_threshold <- quantile(data$avg_salary, 0.75, na.rm = TRUE)
  
  highsalary_summary <- data %>%
    filter(avg_salary >= salary_threshold) %>%   #filter by threshold
    summarise(across(all_of(all_skills), ~ sum(!is.na(.)))) %>%
    pivot_longer(cols = everything(),
                 names_to  = "column",
                 values_to = "count") %>%
    mutate(
      total      = sum(count),
      percentage = count / total * 100
    ) %>%
    arrange(desc(count)) %>%
    mutate(column = factor(column, levels = column))
  
  ggplot(highsalary_summary, aes(x = column, y = count)) +
    geom_col(fill = "thistle") +
    geom_text(
      aes(label = sprintf("%.1f%%", percentage)),
      hjust = -0.1, size = 3.2
    ) +
    scale_y_continuous(expand = expansion(mult = c(0, 0.2))) +
    coord_flip() +
    labs(
      title    = "In Demand Skills for Top 25% Paying Jobs",
      subtitle = paste0("Data for ", nrow(filter(data, avg_salary >= salary_threshold)), 
                        " jobs | Salary threshold: $", round(salary_threshold, 0)),
      x        = NULL,
      y        = "Number of times skill requested in job post"
    ) +
    theme_minimal(base_size = 13) +
    theme(
      plot.title         = element_text(face = "bold"),
      panel.grid.major.y = element_blank()
    )
}

plot_highsalary_skills(jobs_skills)   # call function
```

![](Project.3.Data.Science.Skills.v1_files/figure-commonmark/unnamed-chunk-10-1.png)

# Constraints and Conclusion

During our data transformation & early analysis, we realized that many
of the 8000 job roles in this database did not contain matching skills
data in the “skills” database. For example, of the 1188 job postings
listed in 2020, only 225 had associated skills data when we connected
the two datasets. We hypothesize some job postings did not contain
relevant information or the skills database is incomplete. That said, we
considered alternatives such as generating “skills” for missing rows
based on the data we already have, however this would result in
essentially the same results. Ultimately, we decided to use the data we
had and that there was still enough information to convey interesting
insights and conclusions.

In conclusion, our project used job posting data to explore which data
science skills are most valued. By connecting jobs to skills through a
normalized relational design and analyzing the data in R, we can
identify the most frequently requested skills and compare patterns
across job characteristics such as year, role, and salary. Based on our
findings, there is a slight shift from requesting coding skills such as
R, towards Azure and Computer Vision. That said, R is still among the
skills requested for higher paying positions, on average. And no
surprise, “remote” is the most popular working location for data science
job roles.

# Sources

Claude. (2026). Anthropic \[Large language model\]. Accessed Mar 20,
2026.
[Chat](https://claude.ai/share/e92532a8-a864-4307-b225-fc3a30b9a565)
