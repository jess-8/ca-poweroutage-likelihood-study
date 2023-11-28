# Likelihood of experiencing a wildfire-induced power outage in California

Shenyue Jia | Initiatied on 11/22/2023

A study on the likelihood of experiencing a wildfire-induced power outage in California

## Convert the original poweroutage.us data to long-form

The original data from [poweroutage.us](https://poweroutage.us/) are scrapped from APIs of utility companies at around a 10-minute interval. To make it easier for us to identify continuous temporal sections with continuously high numbers of customers out of power, we can unpack the original data to a long form, which means the time series will contain many zeros due to this dataset's nature.

- R notebook to convert the original data to long-form (01/01/2017 - 12/31/2020)
  - [R notebook](https://htmlpreview.github.io/?https://github.com/jiashenyue/ca-poweroutage-likelihood-study/blob/main/code/00_long_form_data_gen.nb.html)
  - [Long form for each county](https://drive.google.com/drive/folders/1l6SpqOA_7cFBYzVVvuZyZJLr_2ZcGCY7?usp=drive_link)
    - Each 10-min interval contains the number of customers who are out of power within the county
    - Generates data as CSV files
  - [Long form for each city](https://drive.google.com/drive/folders/1k_aXyadT98D4FRbBTB4SQ-9dQYJojwnt?usp=drive_link)
    - City here means census-designated place (CDP), a geographical unit used by U.S. Census Bureau for populous areas
    - Each 10-min interval contains the number of customers who are out of power within the city (CDP)
    - Generates data as [RDS files](https://www.r-bloggers.com/2016/12/remember-to-use-the-rds-format/) to save space and time


## Break long-form data into power outage events

Poweroutage.us dataset creates a duplicated time stamp with the customers out of power as zero when there is no further power outage customer detected in the next 10 min. This is also considered as a flag of the end of a power outage event. We can use this pattern to extract power outage events from the long form we generated.

### How to define a power outage event?

Ambient power outages occur often and not all of them are qualified as a power outage event we are interested in. We can use the following condition to remove the noise in the original long-form time series:

- **Number of customers who are currently out of power > 0.5% of total customers in the area of interest**
- The threshold of power outage changes as the total number of customers changes in the area of interest

- R notebook to identify power outage events (01/01/2017 - 12/13/2020)
  - [Power outage events at the county level](https://drive.google.com/drive/folders/1mD1S8xvQ_8K7czHtK3wyadoOMOudkvU0?usp=drive_link)
    - [R notebook](https://htmlpreview.github.io/?https://github.com/jiashenyue/ca-poweroutage-likelihood-study/blob/main/code/01_generate_outage_events_county.nb.html)
    - One CSV file contains all events at county level for all California counties
  - [Power outage events at the city (CDP) level](https://drive.google.com/drive/folders/1k_aXyadT98D4FRbBTB4SQ-9dQYJojwnt?usp=drive_link)
    - [R notebook](https://htmlpreview.github.io/?https://github.com/jiashenyue/ca-poweroutage-likelihood-study/blob/main/code/03_generate_outage_events_city.nb.html)
    - One CSV file contains all events at city (CDP) level for each county in California

### Pending questions to answer

- A number of cities were lost in data processing as some cities' name cannot be matched with any CDP names, thus cannot have the total housing unit number with it.
- The number of power outage events that last at least 60 min is very small at the city (CDP) level. Not sure if it is related to the loss of city (CDP) level data mentioned above.
  - In contrast, power outage events that last at least 60 min are much more common at the county level. Below is an example:
    - Amador County has 41 county-level power outage events that are at least 60 min long from 2017 to 2020
    - Amador County has 0 cities (CDP)-level power outage events that are at least 60 min long from 2017 to 2020
  - **This needs a more careful investigation**
- (Updated on 11/28) After checking the missing data issue thoroughly in the data, there is no mistake in the table join with census data to obtain a valid housing unit value to define if a power loss event qualifies as a power outage (0.5% total housing unit rule)
- **Further consideration**
  - A different definition for power outage might be needed for CDP level?

### Other known issues

- Some cities in poweroutage.us data are **not** CDPs, thus cannot have a housing unit value to define a power outage event
  - Examples include neighborhoods of a big city, such as `Mar Vista` in `Los Angeles County`
  - A [CSV file](https://github.com/jiashenyue/ca-poweroutage-likelihood-study/blob/main/result/cdp_no_hu_matched.csv) with all cities from poweroutage.us that cannot be joined with a CDP

## Power outages caused by wildfires

## Power outages caused by PSPS
