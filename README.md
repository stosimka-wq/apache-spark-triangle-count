# Spark Triangle Count

A Java application for counting **directed triangular cycles (3-cycles)** in graphs using the Apache Spark RDD API.

## Technologies

- Java 17
- Apache Spark 4.3.0
- Java RDD API (`JavaPairRDD`)
- Windows PowerShell

## Project Structure

```text
Spark-Triangle-Count/
├── src/
│   └── main/
│       └── Java/
│           └── TriangleCount.java
├── data/
│   └── graph.txt
├── .gitignore
└── README.md
```

Generated `.class` and `.jar` files, the `build/` directory, and local IDE settings are excluded through `.gitignore`.

## How It Works

Each non-empty line in `data/graph.txt` contains two vertex IDs separated by whitespace: `a b` represents a **directed edge** `a → b`.

1. Load the edge list with `sc.textFile()` and convert it into a `JavaPairRDD<Integer, Integer>`.
2. Remove self-loops and duplicate edges using `filter()` and `distinct()`.
3. Cache the `edges` RDD because it is reused in the computation.
4. Reverse each edge with `mapToPair()` to prepare for joining consecutive edges.
5. Use the first `join()` to find two consecutive directed edges, `a → b → c`.
6. Use the second `join()` and `filter()` to confirm that `c → a` exists and closes the cycle.
7. Run `count()` and divide by 3: every directed 3-cycle is encountered once from each of its three starting vertices.

## Requirements

- JDK 17 (`java` and `javac` available in the terminal).
- Apache Spark installed with `SPARK_HOME` pointing to its installation directory.
- `spark-submit` available on `PATH`.
- PowerShell on Windows for the commands below.

## Build

Run these commands in **PowerShell from the project root**:

```powershell
New-Item -ItemType Directory -Force -Path "build\classes" | Out-Null
javac -cp "$env:SPARK_HOME\jars\*" -d "build\classes" "src\main\Java\TriangleCount.java"
jar --create --file "build\TriangleCount.jar" -C "build\classes" .
```

## Run

```powershell
spark-submit --class TriangleCount --master "local[*]" "build\TriangleCount.jar" "data\graph.txt"
```

## Example Output

```text
Triangles: 102
```

This is the output observed for the provided `data/graph.txt` dataset.

## Important Note

This implementation counts **directed 3-cycles**, not undirected triangles in general. If an undirected graph is stored with both directions of each edge, a triangle can be counted in both orientations. Undirected triangle counting therefore needs appropriate edge handling and adjusted counting logic.
