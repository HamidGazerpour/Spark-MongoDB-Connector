# Spark MongoDB Connector

A small PySpark/Jupyter notebook project that demonstrates how to connect Apache Spark to MongoDB Atlas, load MongoDB collections as Spark DataFrames, query them with Spark SQL, transform selected columns, persist query results, and write processed data back to MongoDB.

The repository currently contains one notebook:

- [`Spark-Mangodb-connector.ipynb`](Spark-Mangodb-connector.ipynb) - end-to-end Spark and MongoDB Atlas connector workflow.

## What the notebook does

The notebook walks through the following workflow:

1. Starts a PySpark session from Jupyter using `findspark`.
2. Configures Spark with the MongoDB Spark Connector package:
   `org.mongodb.spark:mongo-spark-connector_2.12:2.4.2`.
3. Reads the `listings` and `reviews` collections from a MongoDB Atlas database named `Florence`.
4. Registers both collections as temporary Spark SQL views.
5. Runs exploratory SQL queries against the loaded data.
6. Casts selected listing and review-score fields to numeric Spark SQL types.
7. Saves a transformed query result as a Spark table.
8. Filters listings based on review score conditions.
9. Demonstrates writing a Spark DataFrame back to MongoDB as a new collection.

## Repository structure

```text
.
├── README.md
└── Spark-Mangodb-connector.ipynb
```

## Prerequisites

Install and configure the following before running the notebook:

- Python 3.8+
- Java Development Kit compatible with your Spark version
- Apache Spark installed locally
- Jupyter Notebook or JupyterLab
- A MongoDB Atlas cluster or another MongoDB deployment
- Python packages:
  - `findspark`
  - `pyspark`

You can install the Python dependencies with:

```bash
pip install findspark pyspark notebook
```

> **Note:** The notebook uses MongoDB Spark Connector `2.4.2`, which is intended for older Spark 2.x-style connector APIs such as `com.mongodb.spark.sql.DefaultSource`. If you use a newer Spark version, you may need to update the connector package and read/write format names according to the connector version you choose.

## Configuration

Before running the notebook, update the MongoDB connection strings in the notebook:

```python
input_uri = "mongodb+srv://<username>:<password>@<cluster-url>/?retryWrites=true&w=majority"
ouput_uri = "mongodb+srv://<username>:<password>@<cluster-url>/?retryWrites=true&w=majority"
```

Then confirm the database and collection names match your MongoDB deployment:

```python
.option("database", "Florence")
.option("collection", "listings")
```

and:

```python
.option("database", "Florence")
.option("collection", "reviews")
```

## Running the notebook

1. Clone this repository:

   ```bash
   git clone <repository-url>
   cd Spark-MongoDB-Connector
   ```

2. Install dependencies:

   ```bash
   pip install findspark pyspark notebook
   ```

3. Ensure Spark and Java are available in your environment.

4. Start Jupyter:

   ```bash
   jupyter notebook
   ```

5. Open `Spark-Mangodb-connector.ipynb`.

6. Update the MongoDB connection URI, database, and collection values.

7. Run the notebook cells in order.

## Example Spark session configuration

The notebook configures Spark like this:

```python
myspark = SparkSession \
    .builder \
    .appName("MyApp") \
    .config("spark.mongodb.input.uri", input_uri) \
    .config("spark.mongodb.output.uri", ouput_uri) \
    .config("spark.jars.packages", "org.mongodb.spark:mongo-spark-connector_2.12:2.4.2") \
    .getOrCreate()
```

## Security notes

- Do not commit real usernames, passwords, access tokens, or production connection strings.
- Prefer environment variables, a local untracked configuration file, or a secret manager for credentials.
- If credentials have already been committed, rotate them in MongoDB Atlas before using this project again.
- Restrict MongoDB Atlas network access and database user permissions to the minimum required for your workflow.

## Troubleshooting

### Spark cannot find the MongoDB connector

Make sure the connector package coordinates are compatible with your Spark and Scala versions. The notebook currently uses:

```text
org.mongodb.spark:mongo-spark-connector_2.12:2.4.2
```

### Authentication or connection errors

Check that:

- The MongoDB username and password are correct.
- Your IP address is allowed in MongoDB Atlas Network Access.
- The database user has permissions for the target database and collections.
- The URI is URL-encoded if the password contains special characters.

### Write failures

The final notebook cell writes filtered results back to MongoDB. If the write fails, verify that:

- The output URI is correct.
- The MongoDB user has write permissions.
- The cluster has enough storage available.
- The target database and collection names are valid.
