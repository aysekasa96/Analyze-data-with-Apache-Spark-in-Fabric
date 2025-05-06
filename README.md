# Analyze-data-with-Apache-Spark-in-Fabric
# 📊 Verkenning van Verkoopdata met Apache Spark in Microsoft Fabric

Dit project demonstreert hoe verkoopdata geanalyseerd kan worden met behulp van Apache Spark binnen Microsoft Fabric. Door gebruik te maken van PySpark, hebben we gegevens ingeladen, getransformeerd, geaggregeerd en gevisualiseerd, inclusief opslag in Delta- en Parquet-formaat.

## 🧪 Voorwaarden

- Een Microsoft Fabric trialaccount
- Basiskennis van PySpark en SQL
- Een werkruimte aangemaakt in [Microsoft Fabric](https://app.fabric.microsoft.com)

## 🔧 Projectstructuur

1. **Werkruimte aanmaken**
2. **Lakehouse creëren en CSV-bestanden uploaden**
3. **Notitieboek openen en bewerken**
4. **Gegevens inlezen in een DataFrame**
5. **Data transformeren en opslaan**
6. **Data aggregeren en groeperen**
7. **SQL-query’s uitvoeren**
8. **Visualisaties maken met Matplotlib en Seaborn**

## 📁 Gebruikte Bestanden

De CSV-bestanden zijn te vinden in de map `orders/` en bevatten verkoopgegevens uit:
- 2019.csv
- 2020.csv
- 2021.csv

## 🧬 Data Analyse Stappen

### ✅ Inlezen van data

```python
from pyspark.sql.types import *

orderSchema = StructType([
    StructField("SalesOrderNumber", StringType()),
    StructField("SalesOrderLineNumber", IntegerType()),
    StructField("OrderDate", DateType()),
    StructField("CustomerName", StringType()),
    StructField("Email", StringType()),
    StructField("Item", StringType()),
    StructField("Quantity", IntegerType()),
    StructField("UnitPrice", FloatType()),
    StructField("Tax", FloatType())
])

df = spark.read.format("csv").schema(orderSchema).load("Files/orders/*.csv")
display(df)
🔍 Filteren en groeperen
python
Kopyala
Düzenle
from pyspark.sql.functions import *

yearlySales = df.select(year(col("OrderDate")).alias("Year")).groupBy("Year").count().orderBy("Year")
display(yearlySales)
🛠️ Data transformatie
python
Kopyala
Düzenle
transformed_df = df.withColumn("Year", year(col("OrderDate")))\
                   .withColumn("Month", month(col("OrderDate")))\
                   .withColumn("FirstName", split(col("CustomerName"), " ").getItem(0))\
                   .withColumn("LastName", split(col("CustomerName"), " ").getItem(1))
💾 Opslaan in Parquet en partitioneren
python
Kopyala
Düzenle
transformed_df.write.mode("overwrite").parquet('Files/transformed_data/orders')
orders_df = spark.read.format("parquet").load("Files/transformed_data/orders")

orders_df.write.partitionBy("Year","Month").mode("overwrite").parquet("Files/partitioned_data")
🧮 Werken met SQL
sql
Kopyala
Düzenle
%%sql
SELECT YEAR(OrderDate) AS OrderYear,
       SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue
FROM salesorders
GROUP BY YEAR(OrderDate)
ORDER BY OrderYear;
📊 Visualisaties
from matplotlib import pyplot as plt

df_sales = df_spark.toPandas()
plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
plt.title('Omzet per Jaar')
plt.xlabel('Jaar')
plt.ylabel('Omzet')
plt.show()

📌 Conclusie
Dit project toont hoe krachtige tools zoals Apache Spark en Microsoft Fabric samen kunnen worden gebruikt om inzichten te verkrijgen uit ruwe data. Dankzij DataFrames, SQL, en visualisaties is het eenvoudig om trends te ontdekken, data op te schonen, en deze op te slaan in geoptimaliseerde formaten.
