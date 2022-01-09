# ScratcherStatsNC


{{< figure src="screenshot.png" title="" >}}

## [ScratcherStatsNC Link](https://kevincraig.shinyapps.io/ScratcherStatsNC)

### Motivation
Not all scratch off tickets are created equal. I started this project when I was gifted a bunch of scratchers for my birthday and got all losers, which felt unlikely. The basic idea here is that performance is not equal amongst tickets, and that performance changes through time as people win and lose prizes. By collecting daily lottery data and making a few estimations, I can find which tickets are likely to offer the most bang for your buck. Hope you enjoy, and good luck!

### Data, Viz, & Automation
Data is scraped hourly from [NC Lottery Website](https://nclottery.com/ScratchOff) via an EC2 instance I use for my side projects.

Data is then sent to a Shiny App hosted on shinyapps.io.

The Shiny app is a mix of HTML, R, and Plotly code.

The project was launched in Winter 2020 and continues to update daily. 

