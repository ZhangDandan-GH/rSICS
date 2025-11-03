## Top-$r$ Semantically Important Community Search on Semantic-Rich Heterogeneous Graphs （$r$-SICS）

Official implementation of the VLDB 2026 paper

### 1 Introduction

This repository provides the source code, datasets, and experiment scripts for **$r$-SICS**, a novel community-search framework for semantic-rich heterogeneous information networks (HINs).

### 2 Requirements

The experiments have been run on a Linux server with a 3.7 GHZ, 128 GB memory. All programs are developed using Java 17 and don't need any incorporate additional JAR packages.

We comprehensively evaluated the effectiveness and efficiency of our proposed algorithms on HIN. Table 1 presents six implemented versions of our method, designed to compare the performance under different optimization strategies.  

| **ALGORITHM** | **DESCRIPTION**                                              |
| :-----------: | :----------------------------------------------------------- |
|     Basic     | The basic algorithm based on incremental semantic expansion and refinement. |
|    Opt-BSS    | An optimized algorithm using **B**inary **S**emantic **S**earch to improving the  efficiency of $G_{\rm SMP}$ construction. |
|     Opt-R     | A record-based optimization to reduce repeated SIC construction from $G_{\rm SMP}$ in binary semantic search. |
|   Basic + P   | Parallelized version of the Basic algorithm.                 |
|  Opt-BSS + P  | Parallelized version of Opt-BSS.                             |
|   Opt-R + P   | Parallelized version of Opt-Record.                          |

### 3 Datasets

Our experiment involves four *datasets* popularly deployed by existing works. Each dataset represents a  hetergeneous graph with rich semantics.

The hetergeneous graphs contain graph files, node files, relation files and similarity files, which are stored in the form of txt text. 

Here, DBpedia is taken as an example. 

- node.txt: each line of the node file represents the type of the node, in the form of *dot-blank-dot*.

| node_id | node type |
| :-----: | :-------: |
| 465882  |    336    |

- relation.txt: each line of the relation file represents the ID of the relation except for the first line, in the form of *dot-blank-dot*. The first line of the file records the total number **x** of relations.

| relation name | relation id |
| :-----------: | :---------: |
|   assembly    |     56      |

- similarity.txt: The similarity file recoreds semantic similarity of all relation, organized in grouped segments. Each segment begins with a reference relation in its first line, followed by **x** subsequent lines containing semantic similarity between this reference relation and other relations, in the form of *dot-blank-dot*, sorted in descending order of semantic similarity.

| relation name | semantic similarity |
| :-----------: | :-----------------: |
|    country    |       0.9899        |

- graph.txt: Each row of the graph file represents information of all adjacent nodes and adjacent relations of a node in the form of *source node-blank-adjacent relation 1-blank-adjacent node_id 1-blank-blank-adjacent relation 2-blank-adjacent node_id 2*.

| source node id | adjacent relation 1 | adjacent node id 1 | adjacent relation 2 | adjacent node id 2 |
| :------------: | :-----------------: | :----------------: | :-----------------: | :----------------: |
|    7053036     |      assembly       |       419963       |      assembly       |      3003510       |

### 4 Usage

4.1 Efficiency

We discuss how to obtain the running time of our method. Our proposed methods employ identical parameters: a *relation file path*, a *similarity file path*, a *graph file path*, a *node type file path*, a *query relation*, a *type id* of anchor node, a length bound *l*, a query *k*, a query $r$, a parameter $b$  and the Number of parallel threads # thread. We use Opt-R + P as an example, other methods can be invoked as the same way. To execute  Basic, invoke **Efficiency_Basic**; to execute  Basic_P, invoke **Efficiency_Basic_P**; to execute  Opt_BSS, invoke **Efficiency_Opt_BSS**; to execute  Opt_BSS_P, invoke **Efficiency_Opt_BSS_P**; to execute  Opt_Record, invoke **Efficiency_ Opt_Record**; to execute   Opt_Record_P, invoke **Efficiency_Opt_Record_P**.

```
java -jar core.jar method^
relationFilePath
similarityFilePath 
graphFilePath 
vertexTypeFilePath 
queryRelation 
AaType
queryID 
l
queryK
r
b
threads
```

We provide a reduced *DBpedia* for testing with the following experimental parameters, and  All required files are stored in the **data** folder.

Input

```
java -jar core.jar Efficiency_Opt_P^
  "./data/dbpedia/relation2id.txt" ^
  "./data/dbpedia/similaritytotalfile.txt" ^
  "./data/dbpedia/DbpediaGraphWithPredicate.txt" ^
  "./data/dbpedia/DbpediaVertex.txt" ^
  artist 117 6832424 3 10 3 10 10
```

Output

```
queryRelation: artist
AaType: 117
queryID: 6832424
l: 3
queryK: 10
r: 3
read graph ~~~
read graph over ~~~
b: 10
# threads: 10
Top-1
semantic: [artist, writer]
community size: 110
community nodes: [2970414, 3292726, 4703810,...]
Top-2
semantic: [artist, composer, writer]
community size: 114
community nodes: [2970414, 3292726, 4703810,...]
Top-3
semantic: [artist, composer, associatedBand, writer]
community size: 566
community nodes: [4481092, 2876457, 4640835,...]
Opt-Record+P time:2361.7469ms
```

4.2 Effectiveness

We discuss how to obtain the three most important metrics: NMP, ALSD, and SC. The parameters used by the Effectiveness method are the same as those used in **4.1 Efficiency**. Here is an example of parameter input.

```
java -jar core.jar Effectiveness^
relationFilePath
similarityFilePath 
graphFilePath 
vertexTypeFilePath 
queryRelation 
AaType
queryID 
l
queryK
r
threads
```

We still use the reduced *DBpedia* for testing with the following experimental parameters, and All required files are stored in the **data** folder.

Input

```
java -jar core.jar Effectiveness^
  "./data/dbpedia/relation2id.txt" ^
  "./data/dbpedia/similaritytotalfile.txt" ^
  "./data/dbpedia/DbpediaGraphWithPredicate.txt" ^
  "./data/dbpedia/DbpediaVertex.txt" ^
  artist 117 6832424 3 10 3 10
```

Output

```
queryRelation: artist
AaType: 117
queryID: 6832424
l: 3
queryK: 10
r: 3
read graph ~~~
read graph over ~~~
Top-1
community size: 398
community nodes: [4481092, 2876457, 529420,...]
Top-2
community size: 1515
community nodes: [4481092, 1402901, 5716052, ...]
Top-3
community size: 1567
community nodes: [4481092, 4640835, 5541982, 2773025,...]
ALSD: 0.2777777777777778
NMP: 24
SC: 0.9425733585898489
```

4.3 Case

