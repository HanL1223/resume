---
title: "NYC for hire car Dash Borad"
description: "A end to end data engineering project"
dateString: NOV 2022
draft: false
tags: ["Python","Data engineering", "MAGE"]
showToc: false
weight: 205
cover:
    image: "projects/bankloan_prediction/cover.jpg"
--- 

### link
🔗 [Dashborad](https://lookerstudio.google.com/reporting/04c9ac5c-75fd-4232-ad92-576762ddb091)
### Credit
The dataset is downloaded from[DATABASEURL](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

The reduced version used in this project is upladed as API form [DATAURL](https://storage.googleapis.com/taxi-bucket1/uber_data.csv)
### Skill invlove

**Python**  **MAGE**  **Data engineering**  **Googld Cloud Platform**

## Description
This is a end to end project using a sample data taken from the NYC TLC Trip report invovle using python for initial clearning, using MAGE for construct data pipeline and data visulization using looker studio.

### STEPS
1. To start, I have downloaded the data and uploaded it as a data in google cloud bucket, it can also be made public so we can download it as an API

2. Using build in template from [mage](https://www.mage.ai/) we can apply the following template to extract transform and load the data in to GCP's BIGQuery

3. Then we can perfrom SQL query to shape tables for visulization as needed


## Key Takeaways
- Hands on experience in data engineering.
- Utilized Python for data cleaning and transformation tasks, ensuring data quality and integrity.
- Enhanced understanding of SQL querying and database management, specifically working with GCP's BigQuery.
- Developed in presenting data and insights through interactive dashboards using Looker Studio.(Further improvement require)



- DATA EXTACTION

        import io
        import pandas as pd
        import requests
        if 'data_loader' not in globals():
            from mage_ai.data_preparation.decorators import data_loader
        if 'test' not in globals():
            from mage_ai.data_preparation.decorators import test


        @data_loader
        def load_data_from_api(*args, **kwargs):
            """
            Template for loading data from API
            """
            url = 'https://storage.googleapis.com/taxi-bucket1/uber_data.csv'
            response = requests.get(url)

        return pd.read_csv(io.StringIO(response.text), sep=',')


        @test
        def test_output(output, *args) -> None:
            """
            Template code for testing the output of the block.
            """
            assert output is not None, 'The output is undefined'

- DATA TRANSFORMATION

Here we use the data loaded via api and perfrom data transformation

        import pandas as pd
        if 'transformer' not in globals():
            from mage_ai.data_preparation.decorators import transformer
        if 'test' not in globals():
            from mage_ai.data_preparation.decorators import test


         @transformer

        def transform(df, *args, **kwargs):
            """
            Template code for a transformer block.

        Add more parameters to this function if this block has multiple parent blocks.
        There should be one parameter for each output variable from each parent block.

        Args:
            data: The output from the upstream parent block
            args: The output from any additional upstream blocks (if applicable)

        Returns:
            Anything (e.g. data frame, dictionary, array, int, str, etc.)
        """
        # Specify your transformation logic here
        df['tpep_pickup_datetime'] = pd.to_datetime(df['tpep_pickup_datetime'])
        df['tpep_dropoff_datetime'] = pd.to_datetime(df['tpep_dropoff_datetime'])

        datetime_dim = df[['tpep_pickup_datetime','tpep_dropoff_datetime']].drop_duplicates().reset_index(drop=True)
        datetime_dim['pick_hour'] = datetime_dim['tpep_pickup_datetime'].dt.hour
        datetime_dim['pick_day'] = datetime_dim['tpep_pickup_datetime'].dt.day
        datetime_dim['pick_month'] = datetime_dim['tpep_pickup_datetime'].dt.month
        datetime_dim['pick_year'] = datetime_dim['tpep_pickup_datetime'].dt.year
        datetime_dim['pick_weekday'] = datetime_dim['tpep_pickup_datetime'].dt.weekday

        datetime_dim['drop_hour'] = datetime_dim['tpep_dropoff_datetime'].dt.hour
        datetime_dim['drop_day'] = datetime_dim['tpep_dropoff_datetime'].dt.day
        datetime_dim['drop_month'] = datetime_dim['tpep_dropoff_datetime'].dt.month
        datetime_dim['drop_year'] = datetime_dim['tpep_dropoff_datetime'].dt.year
        datetime_dim['drop_weekday'] = datetime_dim['tpep_dropoff_datetime'].dt.weekday

        datetime_dim['datetime_id'] = datetime_dim.index
        datetime_dim = datetime_dim[['datetime_id', 'tpep_pickup_datetime', 'pick_hour', 'pick_day', 'pick_month', 'pick_year', 'pick_weekday',
                                'tpep_dropoff_datetime', 'drop_hour', 'drop_day', 'drop_month', 'drop_year', 'drop_weekday']]

        passenger_count_dim = df[['passenger_count']].drop_duplicates().reset_index(drop=True)
        passenger_count_dim['passenger_count_id'] = passenger_count_dim.index
        passenger_count_dim = passenger_count_dim[['passenger_count_id','passenger_count']]

        trip_distance_dim = df[['trip_distance']].drop_duplicates().reset_index(drop=True)
        trip_distance_dim['trip_distance_id'] = trip_distance_dim.index
        trip_distance_dim = trip_distance_dim[['trip_distance_id','trip_distance']]
        rate_code_type = {
            1:"Standard rate",
            2:"JFK",
            3:"Newark",
            4:"Nassau or Westchester",
            5:"Negotiated fare",
            6:"Group ride"
        }

        rate_code_dim = df[['RatecodeID']].drop_duplicates().reset_index(drop=True)
        rate_code_dim['rate_code_id'] = rate_code_dim.index
        rate_code_dim['rate_code_name'] = rate_code_dim['RatecodeID'].map(rate_code_type)
        rate_code_dim = rate_code_dim[['rate_code_id','RatecodeID','rate_code_name']]


        pickup_location_dim = df[['pickup_longitude', 'pickup_latitude']].drop_duplicates().reset_index(drop=True)
        pickup_location_dim['pickup_location_id'] = pickup_location_dim.index
        pickup_location_dim = pickup_location_dim[['pickup_location_id','pickup_latitude','pickup_longitude']] 


        dropoff_location_dim = df[['dropoff_longitude', 'dropoff_latitude']].drop_duplicates().reset_index(drop=True)
        dropoff_location_dim['dropoff_location_id'] = dropoff_location_dim.index
        dropoff_location_dim = dropoff_location_dim[['dropoff_location_id','dropoff_latitude','dropoff_longitude']]

        payment_type_name = {
            1:"Credit card",
            2:"Cash",
            3:"No charge",
            4:"Dispute",
            5:"Unknown",
            6:"Voided trip"
        }
        payment_type_dim = df[['payment_type']].drop_duplicates().reset_index(drop=True)
        payment_type_dim['payment_type_id'] = payment_type_dim.index
        payment_type_dim['payment_type_name'] = payment_type_dim['payment_type'].map(payment_type_name)
        payment_type_dim = payment_type_dim[['payment_type_id','payment_type','payment_type_name']]

        fact_table = df.merge(passenger_count_dim, on='passenger_count') \
                .merge(trip_distance_dim, on='trip_distance') \
                .merge(rate_code_dim, on='RatecodeID') \
                .merge(pickup_location_dim, on=['pickup_longitude', 'pickup_latitude']) \
                .merge(dropoff_location_dim, on=['dropoff_longitude', 'dropoff_latitude'])\
                .merge(datetime_dim, on=['tpep_pickup_datetime','tpep_dropoff_datetime']) \
                .merge(payment_type_dim, on='payment_type') \
                [['VendorID', 'datetime_id', 'passenger_count_id',
                'trip_distance_id', 'rate_code_id', 'store_and_fwd_flag', 'pickup_location_id', 'dropoff_location_id',
                'payment_type_id', 'fare_amount', 'extra', 'mta_tax', 'tip_amount', 'tolls_amount',
                'improvement_surcharge', 'total_amount']]
        # Return dictionary like value so we can create 1 table for each combination
        return {"datetime_dim":datetime_dim.to_dict(orient="dict"), 
        "passenger_count_dim":passenger_count_dim.to_dict(orient="dict"),
        "trip_distance_dim":trip_distance_dim.to_dict(orient="dict"),
        "rate_code_dim":rate_code_dim.to_dict(orient="dict"),
        "pickup_location_dim":pickup_location_dim.to_dict(orient="dict"),
        "dropoff_location_dim":dropoff_location_dim.to_dict(orient="dict"),
        "payment_type_dim":payment_type_dim.to_dict(orient="dict"),
        "fact_table":fact_table.to_dict(orient="dict")}


    @test
    def test_output(output, *args) -> None:
        """
        Template code for testing the output of the block.
        """
        assert output is not None, 'The output is undefined'

- DATALOADER

        from mage_ai.data_preparation.repo_manager import get_repo_path
        from mage_ai.io.bigquery import BigQuery
        from mage_ai.io.config import ConfigFileLoader
        from pandas import DataFrame
        from os import path

        if 'data_exporter' not in globals():
            from mage_ai.data_preparation.decorators import data_exporter


        @data_exporter
        
        def export_data_to_big_query(data, **kwargs) -> None:
            """
            Template for exporting data to a BigQuery warehouse.
            Specify your configuration settings in 'io_config.yaml'.

            Docs: https://docs.mage.ai/design/data-loading#bigquery

        
        """
        config_path = path.join(get_repo_path(), 'io_config.yaml')
        config_profile = 'default'
        # K,V pair for each K, generate a table of V
        for key, value in data.items():
            table_id = 'portfolio-pipeline.DEpipeline.{}'.format(key)
            BigQuery.with_config(ConfigFileLoader(config_path, config_profile)).export(
                DataFrame(value),
                print(key,value)
                table_id,
                if_exists='replace',  # Specify resolution policy if table name already exists
            )