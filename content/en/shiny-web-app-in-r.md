---
lang: en
slug: shiny-web-app-in-r
title: Shiny Web App in RStudio
date: 2017-05-25
categories: gfsad
---

After we finished [Global Croplands Extent Products @ 30m](https://croplands.org/app/map?lat=0.17578&lng=0&zoom=2), we need to find a way to release the statistics results for practical purposes. I realized that it is a crucial job to verify and compare cropland area from different products. 

To do that, basically you need a database on web, web server and client to do that data visualization. People like to call them ‘web application framework’, written in Python or Nodejs. From all aspects, it would deplete my memory with a lot of details. In the end, I can always get something to work, just a matter of time cost.

I am sensitive to time cost.  So I give [shiny](https://shiny.rstudio.com/) a try to finish the job yesterday. It cost me 60 minutes and I got [something on-line](gfsad30.shinyapps.io/stat_app/) for demo. People can select region,  x-axis and y-axis to do the investigation and comparison, to generate charts they need. And all the data will be stored in one place for consistency.  I am satisfied with it so far and I feel I can spend some time for further investigation in the future.

Shiny want to keep you stay in R, while they have to present some analyses into interactive web applications. That means firstly it allows you to write web client and server program using R. You needs to learn how to wrap html controls using R, and then wrap the analysis results (usually plots but in reactive objects) for returning. [Shiny’s tutorial](https://shiny.rstudio.com/tutorial/) is a two hours and 25 minutes video with detailed bookmarks to enable you jump into sub-sections. I hope they can provide a text version. I starts with [this exam](https://shiny.rstudio.com/gallery/movie-explorer.html) and [google charts exam](https://shiny.rstudio.com/gallery/google-charts.html) for a skim reading. I used [google charts library](https://developers.google.com/chart/) years ago. They provide rich interactive charts and data tools, which makes plotting less painful.

# Structure of Shiny Application
I’ve created [a github repo](https://github.com/suredream/shiny_stat_app) to host all the files used for this application. A tiny Shiny application can compress into a single R file with following parts:

1. Read your dataframes, preprocessing
1. UI definition (tell shiny how to create a html page to receive inputs thought web widgets)
1. Server logic to take input from web page, processing and organize 

# Dependency and Data prepare
I put some fake statistics into [a csv file](https://github.com/suredream/shiny_stat_app/blob/master/stat_cropland_area.csv). Then I use following command to give the dataframe a grouping field:

```R
library(shiny)
library(dplyr)
library(googleCharts)
data = read.csv('stat_cropland_area.csv')
data$region = as.factor(data$Region)
```

# Define UI for application
I want to display customed scatterplot with different x-axis and y-axis, over different regions I selected. So I add three droplists using:

```R
wellPanel(
            selectInput("cont", "Continents",
                        levels(data$region)
            ),
            selectInput("xvar", "X-axis variable", axis_vars, selected = "gfsad"),
            selectInput("yvar", "Y-axis variable", axis_vars, selected = "mirca"),
            tags$small(paste0(
              "Note: The Statistics are based on GAUL data boundary, which is a spatial database",
              " of the administrative units for all the countries in the world, released by FAO."
            )),
            tags$a(href="http://www.fao.org/geonetwork/srv/en/metadata.show?currTab=simple&id=12691", "Source")
          ),
```

And then the plotting object, wrapped in googleBubbleChart objects:

```R
     googleBubbleChart("chart",
                       width="100%", height = "475px",
                       # Set the default options for this chart; they can be
                       # overridden in server.R on a per-update basis. See
                       # https://developers.google.com/chart/interactive/docs/gallery/bubblechart
                       # for option documentation.
                       options = list(
                         fontName = "Source Sans Pro",
                         fontSize = 13,
                         # Set axis labels and ranges
                         hAxis = list(
                           title = "GFSAD (Mha)"),
                         vAxis = list(
                           title = "MIRCA 2000"),
                         # The default padding is a little too spaced out
                         chartArea = list(
                           top = 50, left = 75,
                           height = "75%", width = "75%"
                         ),
                         # Allow pan/zoom
                         explorer = list(),
                         # Set bubble visual props
                         bubble = list(
                           opacity = 0.4, stroke = "none",
                           # Hide bubble label
                           textStyle = list(
                             color = "none"
                           )
                         ),
                         # Set fonts
                         titleTextStyle = list(
                           fontSize = 16
                         ),
                         tooltip = list(
                           textStyle = list(
                             fontSize = 12
                           )
                         )
                       )
```
 

# Server side work
After uses click and select, a scatterplot will be generated on the server and wrapped in a google chart 
```R
server <- function(input, output, session) {
  
  defaultColors <- c("#3366cc", "#dc3912", "#ff9900", "#109618", "#990099", "#0099c6", "#dd4477")
  series <- structure(
    lapply(defaultColors, function(color) { list(color=color) }),
    names = levels(data$region)
  )
  
  useData <- reactive({
    data %
      filter(region==input$cont) %>%
      select(Country, gfsad, mirca, region) %>%
      arrange(region)
  })
  
  output$chart <- reactive({
    # Return the data and options
    list(
      data = googleDataTable(useData()),
      options = list(
        title = "GFSAD30 vs. MIRCA, %s",
        series = series
      )
    )
  })
}
```

Now, the shiny R code finished.  We can make the application running through:

```R
# Run the application 
shinyApp(ui = ui, server = server)
```

# Deploy on the web, freely
Shiny provides two ways to deploy your application on line: [shiny-server](https://www.rstudio.com/products/shiny/shiny-server/) and [shinyapps.io](http://www.shinyapps.io/). 

Shiny Server lets you put shiny web applications and interactive documents online. Take your Shiny apps and share them with your organization or the world.

Shiny Server lets you go beyond static charts, and lets you manipulate the data. Users can sort, filter, or change assumptions in real-time. Shiny server empower your users to customize your analysis for their specific needs and extract more insight from the data. Open Source Shiny Server provides a platform on which you can host multiple Shiny applications on a single server, each with their own URL or port. It enables you to support non-websocket-enabled browsers like Internet Explorer 8 and 9, and is available under an AGPLv3 license. Shiny Server Pro adds enterprise grade scaling, security, and admin features to the basic open source edition. However, for my work, shinyapps.io is the way to go. I don’t need my own server but upload all my codes and data to RStudio server instead. The free plan allows 5 applications at the same time, and 25 active hours for a month. 

1. Install rsconnect package in R
1. Set up your shinyapps.io account
1. Deploying your application (uploading all your files to shinyapps.io, retrieve the URL)

```R
if(!require("devtools"))
  install.packages("devtools")
devtools::install_github("rstudio/rsconnect")
rsconnect::setAccountInfo(name="">,
                          token="",
                          secret="")
rsconnect::deployApp("")
```

