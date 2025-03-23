# Exploratory-Data-Analysis-on-Covid-19-in-India

India COVID-19 Analysis with PySpark
This project analyzes COVID-19 data from India using PySpark and visualizes the results using Plotly. The dataset contains various COVID-19 statistics for different states/UTs in India, including total cases, active cases, discharged cases, deaths, and more. The project includes data preprocessing, aggregation, and visualization of key statistics.

## Project Setup
This project uses Python, PySpark, and Plotly for analysis and visualization. Follow the steps below to get started with the project.

## Prerequisites
Make sure you have the following installed:
- Python 3.7+
- Apache Spark 3.1.1 (with Hadoop 3.2)
- Java 8
- PySpark
- Plotly

## Installation Steps
1. Clone the repository:

```python
git clone https://github.com/your-username/india-covid-analysis.git
cd india-covid-analysis
```
2. Install necessary dependencies:
- Install Java 8 (if not installed already):
```
sudo apt-get install openjdk-8-jdk-headless -qq > /dev/null
```

- Download and install Spark 3.1.1:
```
wget -q http://archive.apache.org/dist/spark/spark-3.1.1/spark-3.1.1-bin-hadoop3.2.tgz
tar xf spark-3.1.1-bin-hadoop3.2.tgz
```
- Install PySpark and FindSpark:
```
pip install -q findspark
pip install -q pyspark
```
3. Set the environment variables for Java and Spark:
```
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-8-openjdk-amd64"
os.environ["SPARK_HOME"] = "/content/spark-3.1.1-bin-hadoop3.2"
```
4. Start a Spark session:
```
import findspark
findspark.init()

from pyspark.sql import SparkSession
spark = SparkSession.builder.master("local[*]").getOrCreate()
spark.conf.set("spark.sql.repl.eagerEval.enabled", True)
```
5. Download and unzip the dataset:
Upload the file India-covid-data.zip to your working directory.
Unzip the dataset using:
```
!unzip India-covid-data.zip
```
6. Read the dataset:
```
df = spark.read.csv('Latest Covid-19 India Status.csv', inferSchema=True, header=True)
```

## Data Preprocessing
The dataset contains various columns such as Total Cases, Active, Discharged, Deaths, etc. The following preprocessing steps were performed:

1. Renaming columns: Columns with spaces were renamed to have underscores:
```
renamed_df = df.select([F.col(col).alias(col.replace(' ', '_')) for col in df.columns])
```
2. Renaming specific columns: For better clarity:
```
renamed_df = renamed_df.withColumnRenamed('State/UTs', 'State_UTs')
renamed_df = renamed_df.withColumnRenamed('Active_Ratio_(%)', 'Active_Ratio')
renamed_df = renamed_df.withColumnRenamed('Discharge_Ratio_(%)', 'Discharge_Ratio')
renamed_df = renamed_df.withColumnRenamed('Death_Ratio_(%)', 'Death_Ratio')
```
3. SQL queries for data exploration: Example of how to query and explore the data:
```
renamed_df.createOrReplaceTempView('Data')
spark.sql('Select * from Data')
```

## Data Exploration
Explored various aspects of the dataset, such as:
- Counting the number of states/UTs:
```
spark.sql('select count(State_UTs) from Data')
```
- States/UTs with highest active cases:
```
spark.sql('select * from Data order by Active desc limit 5')
```
- States/UTs with highest death ratio:
```
spark.sql('select * from Data order by Death_Ratio desc limit 5')
```
- Total number of COVID cases and deaths:
```
spark.sql('select sum(Total_Cases), sum(Deaths) from Data')
```

## Data Visualization
Used Plotly for visualizing different aspects of the dataset. Key visualizations include:

### Histograms
1. Histogram for Total Cases, Deaths, Active, and Discharged:
```
fig = make_subplots(rows=2, cols=2, subplot_titles=['Total Cases', 'Deaths', 'Active', 'Discharged'])
```
2. Box plots for the same columns:
```
fig = make_subplots(rows=2, cols=2, subplot_titles=['Total Cases', 'Deaths', 'Active', 'Discharged'])
```
3. Bar charts for Total Cases vs. Discharged cases:
```
fig = go.Figure([go.Bar(y=pd_df['Total_Cases'], name='Total Cases', hovertext=pd_df['State_UTs'], x=pd_df['State_UTs']),
                 go.Bar(y=pd_df['Discharged'], name='Discharged', hovertext=pd_df['State_UTs'], x=pd_df['State_UTs'])])
fig.update_layout(barmode='group')
```
4. Pie charts for the Total Cases, Deaths, Active, and Discharged cases for each state:
```
fig = make_subplots(rows=2, cols=2, subplot_titles=['Total Cases', 'Deaths', 'Active', 'Discharged'])
```

Example of the Visualization Code:
```
import plotly.graph_objects as go
import plotly.express as px
from plotly.subplots import make_subplots

fig = make_subplots(rows=2, cols=2, subplot_titles=['Total Cases', 'Deaths', 'Active', 'Discharged'])

total_cases = go.Histogram(x=pd_df['Total_Cases'], nbinsx=20, name='Total Cases')
deaths = go.Histogram(x=pd_df['Deaths'], nbinsx=20, name='Deaths')
active = go.Histogram(x=pd_df['Active'], nbinsx=20, name='Active')
discharged = go.Histogram(x=pd_df['Discharged'], nbinsx=20, name='Discharged')

fig.add_trace(total_cases, 1, 1)
fig.add_trace(deaths, 1, 2)
fig.add_trace(active, 2, 1)
fig.add_trace(discharged, 2, 2)

fig.update_layout(showlegend=True)
fig.show()
```

## Conclusion
This project demonstrates how to use PySpark for data processing and visualization in Python. The analysis highlights key statistics of the COVID-19 pandemic in India, such as active cases, discharged cases, deaths, and their respective ratios across different states/UTs.
