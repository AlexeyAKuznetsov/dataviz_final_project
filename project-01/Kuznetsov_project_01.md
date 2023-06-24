---
title: "Data Visualization Mini Project 1"
author: "Alexey Kuznetsov - `akuznetsov7301@floridapoly.edu`"
output:
  html_document:
    keep_md: true
    df_print: paged
---

## Load and clean data

```{r load-clean-data, warning=FALSE, message=FALSE}
library(tidyverse)

# Load original data using `read_csv()` function
setwd("C:/Users/alexe/OneDrive/Desktop/dataviz_final_project")
Matches_raw <- read_csv("data/WorldCupMatches.csv")

head(Matches_raw)
summary(Matches_raw)
Matches_raw %>%
  group_by(Year) %>%
  summarise(Home_Team_Goals = sum(`Home Team Goals`, na.rm = TRUE),
            Away_Team_Goals = sum(`Away Team Goals`, na.rm = TRUE))

```

## Line Plot

```{r warning=FALSE}

# Group by `Home Team Name` and summarise by the count of matches
home_matches <- Matches_raw %>%
  group_by(`Home Team Name`) %>%
  summarise(Home_Matches = n())

# Group by `Away Team Name` and summarise by the count of matches
away_matches <- Matches_raw %>%
  group_by(`Away Team Name`) %>%
  summarise(Away_Matches = n())

# Create a line plot using `ggplot()` and `geom_line()`
Matches_raw %>%
  group_by(Year) %>%
  summarise(Total_Goals = sum(`Home Team Goals`, na.rm = TRUE) + sum(`Away Team Goals`, na.rm = TRUE)) %>%
  ggplot(aes(x = Year, y = Total_Goals)) +
  geom_line() +
  labs(title = "Total Goals Scored Each Year", x = "Year", y = "Total Goals")

```

## Bar Plot

```{r warning=FALSE}
# Join the home and away matches data, and create a bar plot
total_matches <- full_join(home_matches, away_matches, by = c("Home Team Name" = "Away Team Name"))

total_matches <- total_matches %>%
  mutate(Total_Matches = Home_Matches + Away_Matches)

top_teams <- total_matches %>%
  arrange(desc(Total_Matches)) %>%
  head(10)

top_teams <- top_teams %>%
  filter(!is.na(`Home Team Name`))

ggplot(top_teams, aes(x = reorder(`Home Team Name`, Total_Matches), y = Total_Matches)) +
  geom_col() +
  coord_flip() +
  labs(title = "Number of Matches Played by Top 10 Teams", x = "Team", y = "Number of Matches")
```

## Scatter Plot

```{r warning=FALSE}
# Create a scatter plot using `ggplot()` and `geom_point()`. The scatter plot is used to visualize the relationship between home and away team goals.
Matches_raw$`Home Team Goals` <- as.numeric(Matches_raw$`Home Team Goals`)
Matches_raw$`Away Team Goals` <- as.numeric(Matches_raw$`Away Team Goals`)

ggplot(Matches_raw, aes(x = `Home Team Goals`, y = `Away Team Goals`)) +
  geom_point() +
  scale_x_continuous(breaks = seq(0, max(Matches_raw$`Home Team Goals`, na.rm = TRUE), by = 1)) + # Specify na.rm = TRUE to handle any missing or non-numeric values
  labs(title = "Home Team Goals vs. Away Team Goals", x = "Home Team Goals", y = "Away Team Goals")
```