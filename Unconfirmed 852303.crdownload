from pyspark.sql import SparkSession
from pyspark.ml.feature import StringIndexer, VectorAssembler
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.evaluation import MulticlassClassificationEvaluator

# Start Spark session
spark = SparkSession.builder.appName("BioClassifier").getOrCreate()

# Load dataset
df = spark.read.csv("cleaned_bio_dataset.csv", header=True, inferSchema=True)

# Encode Disease_Status (Healthy/Diseased) to numeric label column
indexer = StringIndexer(inputCol="Disease_Status", outputCol="label")
df = indexer.fit(df).transform(df)

# Select feature columns (all except Disease_Status and label)
feature_cols = [col for col in df.columns if col not in ["Disease_Status", "label"]]

# Assemble features into vector
assembler = VectorAssembler(inputCols=feature_cols, outputCol="features")
df = assembler.transform(df)

# Split data into training and testing sets
train, test = df.randomSplit([0.7, 0.3], seed=42)

# ✅ Check for empty test set
if test.count() == 0:
    print("❌ ERROR: Test set is empty after split. Try adjusting the split ratio or dataset size.")
    exit()

# Create and train logistic regression model
lr = LogisticRegression(featuresCol="features", labelCol="label")
model = lr.fit(train)

# Make predictions on the test set
predictions = model.transform(test)

# ✅ Check if predictions exist
if predictions.filter(predictions.prediction.isNotNull()).count() == 0:
    print("❌ ERROR: No predictions were made. Check input features and model configuration.")
    exit()

# Evaluate accuracy
evaluator = MulticlassClassificationEvaluator(labelCol="label", predictionCol="prediction", metricName="accuracy")
accuracy = evaluator.evaluate(predictions)

# Print accuracy
print(f"\n✅ Model Accuracy: {accuracy:.2%}\n")

# Save accuracy to a text file
with open("result.txt", "w") as f:
    f.write(f"Model Accuracy: {accuracy:.2%}\n")
