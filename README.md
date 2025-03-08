# PySpark_Practise
PySpark concepts and practise

%md
### 1. Spark Architecture Components
> A. Driver Program
✅ What is it?

The entry point of a Spark application.
Runs on the Spark master node and creates the SparkSession.
Converts user-defined operations (e.g., transformations, actions) into jobs.
✅ Responsibilities:

- Maintains SparkContext (interface between application and cluster).
- Splits a job into stages based on transformations.
- Schedules tasks and coordinates execution with executors.
B. Executors
> ✅ What are Executors?

- Worker nodes that run tasks in parallel.
- Each executor is assigned a fraction of data and processes it independently.
**✅ Responsibilities:**

- Executes tasks on assigned data partitions.
- Stores intermediate results in memory (caching) if needed.
- Reports task status and results back to the driver.
### 2. Spark Execution Model
**A. Jobs**

✅ Definition:

- A job is triggered whenever an action is called (e.g., .collect(), .count()).
- The Spark driver splits the job into multiple stages.
✅ Example:


`df.count()  # Triggers a job`
- This job counts rows in the DataFrame.
- Spark converts this into tasks and sends them to executors.

**B. Stages**

✅ Definition:

- A stage is a logical unit of computation determined by transformations.
- Wide transformations (e.g., groupByKey(), reduceByKey()) trigger a shuffle, creating multiple stages.
✅ Example of a Multi-Stage Execution:

`rdd = sc.textFile("data.txt")  # Stage 1

word_counts = rdd.flatMap(lambda line: line.split(" "))  # Stage 1 (narrow transformation)

word_pairs = word_counts.map(lambda word: (word, 1))  # Stage 1 (narrow transformation)

word_freq = word_pairs.reduceByKey(lambda a, b: a + b)  # Stage 2 (wide transformation)

word_freq.collect()  # Stage 2 (Action triggers execution)`

- Stage 1: Reads data, splits into words, and maps them into key-value pairs.
- Stage 2: Triggers a shuffle due to reduceByKey(), creating a new stage.
**C. Tasks**
✅ Definition:

- A task is the smallest unit of execution.
- Each stage is split into multiple tasks, each assigned to a partition.
✅ Execution Flow:

- The driver creates a job and breaks it into stages.
- Each stage consists of multiple tasks.
- Tasks are distributed to executors, where they run in parallel.
- The driver collects results and returns them.
### 3. How Spark’s Execution Model Works
✅ Step-by-Step Breakdown

- User Submits a Spark Job
- The driver program starts execution.
- SparkContext Creates an RDD or DataFrame
- Lazy transformations (e.g., map(), filter()) do not trigger execution.
- Spark Creates a DAG (Directed Acyclic Graph)
- Splits execution into stages.
- Stages Are Divided into Tasks
- Spark assigns tasks to executors based on data locality.
- Executors Process Tasks and Return Results
- Executors store results in memory or disk if necessary.
- Final Result Is Sent to the Driver
- The action (e.g., collect(), count()) returns the computed result.

### 4. DAG (Directed Acyclic Graph) in Spark

✅ What is a DAG?

- A DAG represents the logical execution plan of Spark operations.
- Nodes represent RDD/DataFrame transformations.
- Edges represent dependencies between transformations.

✅ Key Benefits of DAG Execution:

- Optimized execution → Spark reorders operations for efficiency.
- Fault Tolerance → If a task fails, Spark can recompute only the failed partitions.
- Parallelism → Tasks run in parallel across executors.
