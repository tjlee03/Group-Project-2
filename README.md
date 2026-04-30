# Group-Project-2

# Team name
Group 2

# Data Set Description
Our dataset is the **Complete State Energy Data System (SEDS)**, published by the U.S. Energy Information Administration (EIA) and obtained from the U.S. government's open data portal at [data.gov](https://catalog.data.gov/dataset). The EIA's State Energy Data System is the most comprehensive source of state-level energy statistics in the United States, providing historical time series of energy production, consumption, prices, expenditures, and carbon dioxide emissions by state.

The dataset contains **2,364,930 rows** and **5 columns**, spanning **1960 to 2023** across all **50 states**, the District of Columbia, the United States aggregate, and two federal offshore regions (X3 and X5). It encompasses **920 unique Mnemonic Series Name (MSN) codes**, each representing a distinct energy variable such as a specific fuel type consumed by a particular economic sector, measured in a specific unit.

**Data_Status** (String): Indicates the data release cycle. All records in this dataset carry the value "2023F," meaning the data reflects the final release from the 2023 reporting cycle.
- **MSN** (String): A five-character Mnemonic Series Name code that identifies exactly what is being measured. The code encodes three pieces of information: the type of energy (e.g., coal, natural gas, solar, total energy), the type of data or consuming sector (e.g., residential, commercial, industrial, transportation, total consumption), and the unit of measurement (e.g., "B" for billion Btu, "P" for physical units like thousand barrels, "D" for dollars per million Btu, "V" for million dollars). For example, "TERCB" means Total Energy consumed by the Residential sector in Billion Btu, and "SOTCB" means Solar energy Total Consumption in Billion Btu.
- **StateCode** (String): A two-character code representing the U.S. state or region. Standard two-letter postal abbreviations are used for the 50 states and DC. "US" represents the national aggregate, while "X3" and "X5" represent federal offshore regions in the Gulf Coast and West Coast respectively.
- **Year** (Integer): The calendar year the data pertains to, ranging from 1960 to 2023.
- **Data** (Numeric): The measured value, whose unit depends on the MSN code. Because we focused our analysis on MSN codes ending in "B" (billion British thermal units), all of our analytical values share a common unit, enabling direct comparisons across different energy sources and sectors.

Questions and Their Importance

### Question 1: How has energy consumption shifted across the four major economic sectors (residential, commercial, industrial, and transportation) from 1960 to 2023, and how do these sector profiles differ across states?

This question is important because the way a nation distributes its energy consumption across economic sectors reveals fundamental truths about its economic structure, development trajectory, and quality of life. The United States underwent one of the most significant economic transformations in modern history during the period covered by this dataset. We shifted from a from a manufacturing-driven industrial economy to a service and technology-oriented economy. Energy consumption patterns reflect this structural change in a way that GDP figures alone cannot capture.

From a social perspective, residential energy consumption reflects how Americans heat, cool, and power their homes, which ties directly to housing standards, climate adaptation, and household costs. From an economic perspective, the decline in industrial energy's share signals deindustrialization, offshoring of manufacturing, and gains in industrial energy efficiency. The growth of transportation energy reveals increased vehicle dependence and freight movement. From a policy perspective, understanding which sectors consume the most energy in each state informs where efficiency programs, carbon reduction mandates, and infrastructure investments should be directed.

This question is directly tied to the SEDS dataset through the MSN codes TERCB (residential), TECCB (commercial), TEICB (industrial), TEACB (transportation), and TETCB (total), which provide annual consumption figures for each sector in a consistent unit (billion Btu) across all states and years.

### Question 2: How has the share of renewable energy in total U.S. energy consumption changed over time, and which states are leading or lagging in the transition from fossil fuels to renewable energy sources?

This question is important because the transition from fossil fuels to renewable energy is one of the most consequential economic, environmental, and political challenges of our time. Climate change, driven in large part by fossil fuel combustion, poses risks to agriculture, infrastructure, public health, and national security. Understanding where the United States stands in its energy transition, such as which states are driving progress versus which remain dependent on fossil fuels are critical insights for policymakers, investors, and citizens alike.

From an environmental perspective, the growth of renewable energy directly reduces greenhouse gas emissions and air pollution. From an economic perspective, the renewable energy sector has become a major source of jobs, capital investment, and technological innovation, and states that lead in renewable adoption attract clean energy manufacturing and development. From a social equity perspective, energy costs and environmental burdens from fossil fuel extraction disproportionately affect low-income communities, making the pace of transition a matter of social justice.

This question is tied to the dataset through the MSN codes RETCB (total renewable consumption), FFTCB (total fossil fuel consumption), TETCB (total energy consumption), SOTCB (solar), WYTCB (wind), HYTCB (hydropower), BMTCB (biomass), GETCB (geothermal), CLTCB (coal), NNTCB (natural gas), and PMTCB (petroleum). These codes enable us to track both aggregate trends and granular breakdowns by energy source across states and years.


# Manipulations

## Data Manipulations

The SEDS dataset presented a challenges because of its structure. Rather than containing separate columns for each measure (e.g., one column for residential energy, another for commercial energy), all values are stored in a single "Data" column, with the MSN code identifying what each value represents. This means that a single row might contain residential energy consumption in billion Btu, while the next row for the same state and year might contain crude oil production in thousand barrels. Mixing these values without careful filtering would produce meaningless results.

To address this, we performed the following manipulations in Tableau:

**1. MSN Code Filtering.** We filtered the dataset from 920 MSN codes down to approximately 15–20 codes relevant to our two questions. For Question 1, we used TERCB, TECCB, TEICB, TEACB, and TETCB. For Question 2, we used RETCB, FFTCB, TETCB, SOTCB, WYTCB, HYTCB, BMTCB, GETCB, CLTCB, NNTCB, and PMTCB. All selected codes end in "B" (billion Btu), ensuring all values share a common unit of measurement and can be compared directly.

**2. Conditional Calculated Fields.** Because the dataset stores all values in one column, we used Tableau's IF-THEN logic to create individual measures from the stacked data. For example, the calculated field for residential energy consumption was written as: `SUM(IF [MSN] = "TERCB" THEN [Data] END)`. We created similar calculated fields for each energy source and sector. This ended up being about 17 fields total. This effectively "pivoted" the long-format data into usable individual measures within Tableau without needing to restructure the underlying CSV file.

**3. Percentage Share Calculations.** To analyze how each sector's proportion of total energy has changed over time, we created share calculated fields by dividing each sector's consumption by the total. For example, Industrial Share was calculated as: `SUM(IF [MSN] = "TEICB" THEN [Data] END) / SUM(IF [MSN] = "TETCB" THEN [Data] END) * 100`. The same approach was used to calculate renewable energy's share of total consumption. These share calculations normalize the data and reveal structural shifts that raw consumption figures alone would obscure.

**4. Per Capita Normalization.** To compare energy consumption across states fairly (since Texas naturally consumes more than Vermont simply due to population), we created a per capita calculated field: `SUM(IF [MSN] = "TETCB" THEN [Data] END) / SUM(IF [MSN] = "TPOPP" THEN [Data] END)`. The TPOPP code provides state population in thousands, enabling us to identify which states are genuinely energy-intensive on a per-person basis versus simply large.

**5. Energy Intensity Calculation.** To examine economic efficiency of energy use, we divided total energy consumption by real GDP: `SUM(IF [MSN] = "TETCB" THEN [Data] END) / SUM(IF [MSN] = "GDPRX" THEN [Data] END)`. This measures how many Btu are required to produce one dollar of economic output, revealing whether the economy is becoming more energy-efficient over time.

**6. Geographic and Temporal Filtering.** We excluded the aggregate codes "US," "X3," and "X5" from state-level analyses to avoid double-counting, and filtered to "US" only for national trend analyses. For certain visualizations, we filtered to specific year ranges (e.g., 2005–2023 for the Georgia coal-to-solar analysis) to highlight the most relevant time periods.


# Analysis and Results

### Question 1 Findings: Sector Consumption Shifts

Our analysis reveals a dramatic structural transformation in how the United States consumes energy.

**The decline of industrial dominance.** In 1970, the industrial sector accounted for 43.7% of all U.S. energy consumption — nearly half of the nation's total. By 2023, that share had fallen to 33.0%, a drop of over 10 percentage points. This decline reflects the broader deindustrialization of the American economy as manufacturing moved offshore and domestic industry adopted more energy-efficient technologies and processes.

**The rise of commercial energy.** The commercial sector grew from 12.0% of total consumption in 1970 to 17.4% in 2023. This 5.4 percentage point increase corresponds to the expansion of the U.S. service economy — the growth of office buildings, data centers, retail spaces, hospitals, and schools that now form the backbone of economic activity.

**Transportation's growing share.** Transportation energy rose from 24.4% to 30.0% of the total over the same period, driven by increased vehicle ownership, suburban sprawl, freight growth, and aviation expansion. Despite improvements in vehicle fuel efficiency, the sheer growth in miles traveled has pushed the sector's share higher.

**Residential stability.** The residential sector has remained remarkably stable, shifting only from 19.9% to 19.6% between 1970 and 2023. This consistency masks competing trends: homes have grown larger and use more appliances, but insulation, building codes, and appliance efficiency standards have offset much of that growth.

**COVID-19 as a natural experiment.** The 2020 pandemic provided an involuntary test of how each sector responds to disruption. Transportation consumption plummeted by 14.8% as travel halted. Commercial consumption dropped 7.8% as offices and businesses closed. Industrial fell 4.5%. Residential declined only 4.0%, as people stayed home and shifted energy use from commercial buildings to their houses. These differential impacts highlight how interconnected energy consumption is with daily economic and social activity.

**State-level variation tells an economic story.** When we examined individual states, we found striking differences in sector profiles that map directly to each state's economy. Louisiana's energy consumption is 69.9% industrial — reflecting its dominance in petroleum refining and petrochemical manufacturing. Hawaii is 59.1% transportation because nearly all goods and fuel must be shipped or flown to the islands. California is 44.5% transportation due to its car-dependent urban design and massive freight corridors. Connecticut and Rhode Island are over 32% residential, reflecting their cold winters, small geographic size, and service-oriented economies with little heavy industry.

**Per capita consumption reveals hidden patterns.** Raw consumption figures are misleading because they reflect population size. Our per capita analysis revealed that Alaska (1,013 billion Btu per thousand people), Louisiana (908), North Dakota (892), and Wyoming (854) consume four to six times more energy per person than Rhode Island (166), California (174), or New York (174). The high per capita states are dominated by energy extraction, refining, and heavy industry. The low per capita states tend to have service-based economies, milder climates, and denser populations.

<img width="614" height="408" alt="image" src="https://github.com/user-attachments/assets/6a79f706-044f-4852-a0e2-9d2351e2713b" />
<img width="635" height="406" alt="image" src="https://github.com/user-attachments/assets/3bed3682-202b-4831-9463-e2b9949561c1" />
<img width="643" height="374" alt="image" src="https://github.com/user-attachments/assets/cce5d33a-9123-46e3-aee4-4143c3cc107b" />
<img width="640" height="401" alt="image" src="https://github.com/user-attachments/assets/5704b290-26f0-46f8-8b83-d23744530a00" />
*(Supporting visualizations: Sector Trend Stacked Area Chart, Sector Share Stacked Area Chart, Per Capita Bar Chart by State, State Sector Profiles Stacked Bar Chart)*

### Question 2 Findings: The Renewable Energy Transition

Our analysis reveals that while renewable energy is growing, the transition is uneven and fossil fuels still dominate.

**Slow but accelerating growth in renewables.** Renewable energy's share of total U.S. energy consumption was 3.5% in 1970. It remained below 5% for decades, only reaching 4.2% by 2000. The acceleration began in the 2010s, climbing to 6.2% by 2010, 8.2% by 2020, and 8.8% by 2023. While 8.8% may seem modest, the rate of growth has been increasing each decade, suggesting the transition is entering an exponential phase.

**Wind and solar are driving the growth.** The composition of renewable energy has transformed dramatically. In 2005, solar contributed only 51,939 billion Btu and wind contributed 60,770 billion Btu to U.S. consumption. By 2023, solar had grown to 880,325 billion Btu (a roughly 17-fold increase) and wind to 1,436,934 billion Btu (a roughly 24-fold increase). Meanwhile, hydropower has remained relatively flat at 835,948 billion Btu in 2023, and biomass — which still accounts for 60.0% of all renewable energy — has grown modestly. Geothermal remains a small contributor at 1.5% of renewables. The growth story is overwhelmingly a wind and solar story.

**The coal-to-gas switch is the biggest fossil fuel story.** Within fossil fuels, coal consumption dropped by 64.2% between 2005 and 2023, from 22.8 million billion Btu to 8.2 million. Natural gas surged by 48.9% over the same period, from 22.6 million to 33.6 million billion Btu. Petroleum declined by 11.8%. The displacement of coal by natural gas — driven by the shale gas revolution and lower natural gas prices — has been the single largest factor in reducing U.S. carbon emissions over the past two decades, even more than the growth of renewables.

**State-level leaders and laggards.** The renewable energy transition is not happening uniformly. South Dakota leads the nation with 36.4% of its energy coming from renewable sources, followed by Iowa at 29.5%, Maine at 27.2%, and Oregon at 27.0%. These leaders are driven by different renewable sources: South Dakota and Iowa by wind power, Oregon and Washington by hydropower, Maine by biomass. At the bottom, Alaska (1.4%), DC (2.1%), Delaware (3.0%), and Louisiana (3.3%) have minimal renewable penetration. The bottom states are generally fossil fuel producers or small, densely populated areas without geographic resources for wind or hydro.

**The biggest gains since 2000.** The states that have made the most progress in increasing their renewable share are South Dakota (from 10.9% to 36.4%, a gain of 25.6 percentage points), Iowa (from 6.0% to 29.5%, a gain of 23.5 points), and Kansas (from 0.9% to 14.4%, a gain of 13.5 points). All three are Great Plains states where wind energy development has been most concentrated, reflecting the geographic concentration of the energy transition.

**Georgia as a case study.** Georgia illustrates the coal-to-solar transition at the state level. In 2005, Georgia consumed 901,010 billion Btu of coal. By 2023, that figure had fallen to 177,521 — an 80% decline. Meanwhile, solar energy in Georgia exploded from just 1,064 billion Btu in 2014 to 27,863 in 2023, a 26-fold increase in under a decade. Georgia is also notable as a nuclear energy state, with nuclear power providing 13.8% of its total energy consumption, partly due to the Plant Vogtle expansion — one of the only new nuclear plants built in the United States in the 21st century.

<img width="612" height="410" alt="image" src="https://github.com/user-attachments/assets/54fd2c3b-cb4d-45d2-890e-deba379b3bfd" />
<img width="664" height="415" alt="image" src="https://github.com/user-attachments/assets/3f42c39d-75ab-4232-a5b1-8e6f2c8fbaa0" />
<img width="704" height="415" alt="image" src="https://github.com/user-attachments/assets/2d0be181-0d9b-43b0-b993-3077d2a1a1cd" />
<img width="695" height="410" alt="image" src="https://github.com/user-attachments/assets/090a20a6-de1f-4e34-9018-9b12d1449cfd" />
<img width="697" height="407" alt="image" src="https://github.com/user-attachments/assets/02df1f05-3ab8-4635-9afd-b3f5ccb41a6c" />

*(Supporting visualizations: Renewable Share Bar Chart by State, Renewable Breakdown Stacked Bar Chart, Fossil Fuel Breakdown Area Chart, Georgia Coal vs Solar Dual Axis Chart, Energy Intensity Line Chart)


# Tableau Packaged Workbook
It is attached above the readme. 
