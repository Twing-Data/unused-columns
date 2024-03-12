# unused-columns

A small script to help identify unused columns by analyzing the query history and comparing the extracted columns against the information schema.

## Usage

```bash
~ python identify_unused_columns.py --query_file sample_queries.csv --info_schema_file sample_info_schema.csv
Read 63 queries from sample_queries.csv
Read 116 information schema rows from sample_info_schema.csv
Most common columns (20):
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'YMD'): 30
('CATALOG', 'PUBLIC', 'BRAND', 'NAME'): 27
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'BRAND_ID'): 22
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'YMD'): 20
('CATALOG', 'PUBLIC', 'CITY', 'NAME'): 16
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'REGION_ID'): 15
('CATALOG', 'PUBLIC', 'REGION', 'NAME'): 14
('CATALOG', 'PUBLIC', 'BRAND', 'ID'): 14
('CATALOG', 'PUBLIC', 'AGG_HOURLY', 'YMD'): 13
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'CONVERSIONS'): 12
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'OS_ID'): 12
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'CITY_ID'): 12
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'IMPRESSIONS'): 11
('CATALOG', 'PUBLIC', 'CITY', 'ID'): 10
('CATALOG', 'PUBLIC', 'AD_REQUEST', 'AD_REQUEST_ID'): 10
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'SPEND'): 10
('CATALOG', 'PUBLIC', 'AGG_HOURLY', 'BRAND_ID'): 9
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'CLICKS'): 9
('CATALOG', 'PUBLIC', 'AGG_DAILY', 'BROWSER_ID'): 9
('CATALOG', 'PUBLIC', 'AGG_HOURLY', 'REGION_ID'): 8
Unused columns (27):
('CATALOG', 'PUBLIC', 'AD_CLICK', 'DATETIME')
('CATALOG', 'PUBLIC', 'AD_COMBINED', 'AD_REQUEST_ID')
('CATALOG', 'PUBLIC', 'AD_CONVERSION', 'DATETIME')
('CATALOG', 'PUBLIC', 'AD_VIEW', 'DATETIME')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'BROWSER_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'COUNTRY_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'CREATIVE_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'OS_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'POSTAL_CODE')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY', 'PUBLISHER_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'BRAND_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'BROWSER_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'CITY_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'CLICKS')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'CONVERSIONS')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'COST')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'COUNTRY_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'CREATIVE_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'IMPRESSIONS')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'OS_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'POSTAL_CODE')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'PUBLISHER_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'REGION_ID')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'SPEND')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'VIEWS')
('CATALOG', 'PUBLIC', 'AGG_MONTHLY_V2', 'YMD')
('CATALOG', 'PUBLIC', 'CREATIVE', 'NAME')
```

## Getting the query history

This fetches the 100 most recent queries from the query history that affect columns. If you want to expand the set you will likely need to modify the script to support parsing other query types.

```sql
select query_text, database_name, schema_name
from snowflake.account_usage.query_history
where query_type in (
    'DELETE',
    'INSERT',
    'SELECT',
    'UPDATE'
    )
and query_text != ''
order by start_time desc limit 100;
```

## Getting the information schema

Filter out the information schema. Feel free to tweak the where clause to filter to what you want to focus on.

```sql
select table_catalog, table_schema, table_name, column_name
from information_schema.columns
where table_schema != 'INFORMATION_SCHEMA'
```
