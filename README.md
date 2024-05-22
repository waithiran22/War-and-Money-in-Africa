# War and Money in Africa
Analysis of Economic Indicators and Conflict Events in Africa. In this project, we analyzed the relationship between economic indicators and conflict events in African countries from 1997 to 2018. We focused on a representative sample of three countries from each major region in Africa to maintain a manageable scope while ensuring diversity. The countries were chosen based on their geopolitical significance and availability of comprehensive data. Data was sourced from Kaggle datasets: "Political Conflicts in Africa from 1997-2018" by Roberto Berwa and "African Economy from 1980 to 2022" by MahmoudSaeed.

## Load and Clean Data
```
conflicts <- read_excel("DATA 332/Final Project/conflicts.xlsx")
africa_economy <- read_excel("DATA 332/Final Project/africa_economy.xlsx")
```
## Clean Data: Replace Empty Strings and Handle Missing Values
```
# Replace empty strings with NA for conflicts data
conflicts <- conflicts %>%
  mutate(across(where(is.character), ~ na_if(.x, "")))

# Replace empty strings with NA for africa_economy data
africa_economy <- africa_economy %>%
  mutate(across(where(is.character), ~ na_if(.x, "")))

# Check for and handle missing values
conflicts <- conflicts %>%
  drop_na()

africa_economy <- africa_economy %>%
  drop_na()

# Remove duplicate rows
conflicts <- conflicts %>%
  distinct()

africa_economy <- africa_economy %>%
  distinct()
```
## Standardize Country Names
```
Consistency in country names (remove leading/trailing spaces, consistent capitalization)
conflicts <- conflicts %>%
  mutate(COUNTRY = str_trim(COUNTRY),
         COUNTRY = str_to_title(COUNTRY))

africa_economy <- africa_economy %>%
  mutate(Country = str_trim(Country),
         Country = str_to_title(Country))
```
## Define and Assign Regions
```
# Define regions and assign to countries
north_africa <- c("Egypt", "Algeria", "Morocco")
east_africa <- c("Kenya", "Ethiopia", "Uganda")
west_africa <- c("Nigeria", "Ghana", "Senegal")
south_africa <- c("South Africa", "Namibia", "Botswana")
central_africa <- c("Cameroon", "Chad", "Central African Republic")

conflicts <- conflicts %>%
  mutate(Region = case_when(
    COUNTRY %in% north_africa ~ "North Africa",
    COUNTRY %in% east_africa ~ "East Africa",
    COUNTRY %in% west_africa ~ "West Africa",
    COUNTRY %in% south_africa ~ "South Africa",
    COUNTRY %in% central_africa ~ "Central Africa",
    TRUE ~ "Other"
  ))

africa_economy <- africa_economy %>%
  mutate(Region = case_when(
    Country %in% north_africa ~ "North Africa",
    Country %in% east_africa ~ "East Africa",
    Country %in% west_africa ~ "West Africa",
    Country %in% south_africa ~ "South Africa",
    Country %in% central_africa ~ "Central Africa",
    TRUE ~ "Other"
  ))

# Filter out "Other" regions
conflicts <- conflicts %>% filter(Region != "Other")
africa_economy <- africa_economy %>% filter(Region != "Other")
```

## Aggregate Data by Region and Decade
```
# Aggregate conflict data by region and decade
conflicts_summary <- conflicts %>%
  group_by(Region, Decade) %>%
  summarize(
    Total_Fatalities = sum(FATALITIES, na.rm = TRUE),
    Total_Events = n(),
    ACTOR1 = first(ACTOR1),  
    ACTOR2 = first(ACTOR2),
    .groups = 'drop'
  )

# Aggregate economy data by region and decade
africa_economy_summary <- africa_economy %>%
  group_by(Region, Decade) %>%
  summarize(
    Avg_GDP_Growth = mean(`Real GDP growth (annual %)`, na.rm = TRUE),
    Avg_Inflation = mean(`Inflation, consumer prices (annual %)`, na.rm = TRUE),
    .groups = 'drop'
  )
```
## Merge and Save Cleaned Data
```
# Merge the datasets
data <- conflicts_summary %>%
  left_join(africa_economy_summary, by = c("Region", "Decade"))

# Save cleaned data
saveRDS(conflicts_summary, "cleaned_conflicts_summary.rds")
saveRDS(africa_economy_summary, "cleaned_africa_economy_summary.rds")
saveRDS(data, "cleaned_data.rds")
```
### SHINY APP
## Define UI
```
# Define UI
ui <- fluidPage(
  tags$head(
    tags$style(HTML("
      body {
        background-color: purple;
        color: black;
        font-family: 'Times New Roman', Times, serif;
        font-size: 16px;
      }
      h1, h2, h3, p {
        font-family: 'Times New Roman', Times, serif;
        font-weight: bold;
      }
      h1 {
        font-size: 26px;
      }
      h2 {
        font-size: 24px;
      }
      h3 {
        font-size: 22px;
      }
    "))
  ),
  titlePanel("War and Money in Africa"),
  
  sidebarLayout(
    sidebarPanel(
      h1("Analysis of Economic Indicators and Conflict Events in Africa"),
      p("Presented by Waithira Ng'ang'a and Ema kidan")
    ),
    
    mainPanel(
      tabsetPanel(
        tabPanel("Project Overview",
                 p("This project aims to analyze the relationship between economic indicators and conflict events in African countries from 1997 to 2018. The motivation for this research stems from the desire to understand how economic factors influence the frequency and severity of conflicts in the region.")
        ),
        tabPanel("Data Visualization", 
                 tabsetPanel(
                   tabPanel("Fatalities",
                            h3("Total Fatalities by Region and Decade"),
                            plotOutput("fatalitiesPlot")
                   ),
                   tabPanel("Events",
                            h3("Total Events by Region and Decade"),
                            plotOutput("eventsPlot")
                   ),
                   tabPanel("GDP Growth",
                            h3("Average GDP Growth by Region and Decade"),
                            plotOutput("gdpPlot")
                   ),
                   tabPanel("Inflation",
                            h3("Average Inflation by Region and Decade"),
                            plotOutput("inflationPlot")
                   ),
                   tabPanel("GDP vs Conflict Fatalities",
                            h3("Correlation Between GDP Growth and Conflict Fatalities"),
                            plotOutput("gdpConflictPlot")
                   ),
                   tabPanel("Inflation vs Conflict Events",
                            h3("Correlation Between Inflation and Conflict Events"),
                            plotOutput("inflationConflictPlot")
                   )
                 )
        ),
        tabPanel("Geospatial Visualization",
                 h3("Geospatial Visualization"),
                 leafletOutput("map", height = 500)
        ),
        tabPanel("Documentation", 
                 h3("Documentation"),
                 p("Berwa, Roberto. “Political Conflicts in Africa from 1997-2018.” Kaggle.com, 2018, www.kaggle.com/datasets/robertoberwa/conflicts-in-africa-from-19972018. Accessed 22 May 2024.
"),
                 p("MahmoudSaeed. “African Economy from 1980 to 2022.” Kaggle.com, 2022, www.kaggle.com/datasets/mahmoudsaeed99/african-economy-from-1980-to-2022. Accessed 22 May 2024.
"),
                 p("All citated busing MLA 9")
        ),
        tabPanel("References", 
                 h3("References"),
                 p("Cite any external sources, including LLM guidance and journal articles, in APA format.")
        )
      )
    )
  )
)
```
### Define Server Logic
```
# Define server logic
server <- function(input, output) {
  output$fatalitiesPlot <- renderPlot({
    ggplot(conflicts_summary, aes(x = Decade, y = Total_Fatalities, fill = Region)) +
      geom_bar(stat = "identity", position = "dodge") +
      labs(title = "Total Fatalities by Region and Decade", x = "Decade", y = "Total Fatalities") +
      theme_minimal() +
      scale_fill_brewer(palette = "Set3")
  })
  
  output$eventsPlot <- renderPlot({
    ggplot(conflicts_summary, aes(x = Decade, y = Total_Events, fill = Region)) +
      geom_bar(stat = "identity", position = "dodge") +
      labs(title = "Total Events by Region and Decade", x = "Decade", y = "Total Events") +
      theme_minimal() +
      scale_fill_brewer(palette = "Set3") +
      facet_wrap(~ Region)
  })
  
  output$gdpPlot <- renderPlot({
    ggplot(africa_economy_summary, aes(x = Decade, y = Avg_GDP_Growth, color = Region, group = Region)) +
      geom_line(size = 1.2) +
      geom_point(size = 3) +
      labs(title = "Average GDP Growth by Region and Decade", x = "Decade", y = "Average GDP Growth (%)") +
      theme_minimal() +
      scale_color_brewer(palette = "Set1") +
      facet_wrap(~ Region)
  })
  
  output$inflationPlot <- renderPlot({
    ggplot(africa_economy_summary, aes(x = Decade, y = Avg_Inflation, color = Region, group = Region)) +
      geom_line(size = 1.2) +
      geom_point(size = 3) +
      labs(title = "Average Inflation by Region and Decade", x = "Decade", y = "Average Inflation (%)") +
      theme_minimal() +
      scale_color_brewer(palette = "Set1") +
      facet_wrap(~ Region)
  })
  
  output$gdpConflictPlot <- renderPlot({
    combined_data <- conflicts_summary %>%
      left_join(africa_economy_summary, by = c("Region", "Decade"))
    
    ggplot(combined_data, aes(x = Avg_GDP_Growth, y = Total_Fatalities, color = Region)) +
      geom_point(size = 3) +
      geom_smooth(method = "lm", se = FALSE) +
      labs(title = "Correlation Between GDP Growth and Conflict Fatalities", x = "Average GDP Growth (%)", y = "Total Fatalities") +
      theme_minimal() +
      scale_color_brewer(palette = "Set1") +
      facet_wrap(~ Region)
  })
  
  output$inflationConflictPlot <- renderPlot({
    combined_data <- conflicts_summary %>%
      left_join(africa_economy_summary, by = c("Region", "Decade"))
    
    ggplot(combined_data, aes(x = Avg_Inflation, y = Total_Events, color = Region)) +
      geom_point(size = 3) +
      geom_smooth(method = "lm", se = FALSE) +
      labs(title = "Correlation Between Inflation and Conflict Events", x = "Average Inflation (%)", y = "Total Events") +
      theme_minimal() +
      scale_color_brewer(palette = "Set1") +
      facet_wrap(~ Region)
  })
  
  output$map <- renderLeaflet({
    leaflet(conflict_data) %>%
      addProviderTiles(providers$OpenStreetMap.Mapnik) %>%
      addCircleMarkers(
        ~longitude, ~latitude,
        radius = ~sqrt(Total_Fatalities) / 20,
        color = ~case_when(
          Region == "North Africa" ~ "red",
          Region == "East Africa" ~ "green",
          Region == "West Africa" ~ "blue",
          Region == "South Africa" ~ "yellow",
          Region == "Central Africa" ~ "purple"
        ),
        opacity = 0.5, fillOpacity = 0.5,
        popup = ~paste("Country:", COUNTRY, "<br>",
                       "Region:", Region, "<br>",
                       "Total Fatalities:", Total_Fatalities, "<br>",
                       "Decade:", Decade, "<br>",
                       "Actor 1:", ACTOR1, "<br>",
                       "Actor 2:", ACTOR2)
      ) %>%
      addLegend(
        position = "bottomright",
        colors = c("red", "green", "blue", "yellow", "purple"),
        labels = c("North Africa", "East Africa", "West Africa", "South Africa", "Central Africa"),
        title = "Regions"
      )
  })
}

# Run the application 
shinyApp(ui = ui, server = server)
```

