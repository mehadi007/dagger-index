# dagger-index
Automatically exported from code.google.com/p/dagger-index

# DAGGER: Reachability Index for Dynamic Graphs #
This software is a C++ implementation of the methods proposed in the paper:

> Hilmi Yildirim, Vineet Chaoji and Mohammed J. Zaki : "_DAGGER: A Scalable Index for Reachability Queries in Large Dynamic Graphs_", CoRR (Arxiv) abs/1301.0977, 2013 (http://arxiv.org/abs/1301.0977).

Please cite the papers if you use the software for academic purposes. You can download the whole package from [Downloads](http://code.google.com/p/dagger-index/downloads/list) or svn checkout from [Source](http://code.google.com/p/dagger-index/source/checkout) tab.

Please read ([README](README.md)) for the proper usage and instructions for installation.

Please see [SCRIPTS](SCRIPTS.md) for getting some statistics and plots from DAGGER.

## Disclaimer: ##
The software is provided on an as is basis for research purposes and licensed under [GNU GPL v3](http://www.gnu.org/licenses/gpl.html). There is no additional support offered, nor are the author(s) or their institutions liable under any circumstances.

## Acknowledgements: ##
This work was supported in part by NSF Grant EMT-0829835, and NIH Grant 1R01EB0080161-01A1.

--------------------------------------------------------------

## Contents ##
The package is available as a compressed **DAGGER** folder in [Downloads](http://code.google.com/p/dagger-index/downloads/list) and as browsable files in [Source](http://code.google.com/p/dagger-index/source/browse). Once compiled, the package provides the following executables:

  1. **DGRAIL**: The main executable which evaluates the performance of DAGGER, DFS or BFS on a given input graph and an _update file_ (a file that lists the changes to be applied to the graph).
  1. **correctness**: It checks the correctness of DAGGER/DFS/BFS by comparing the result of each query on a given input graph and update file.
  1. **Graph Generators**: Random graph generators.
    * **erGenerator**: Generates a random graph in Erdos-Renyi Model.
    * **baGenerator**: Generates a random graph in Barabasi-Albert Model.
  1. **testGenerator**: Given an input graph, it generates a random sequence of valid update operations which can be used to evaluate reachability indices.
  1. **Graph Extractors**: These extracts an input(i.e., initial) graph from a list of updates and outputs in .gra format. The reason we need this tool is that in some cases updates are available from an empty graph but we want to simulate the evolution of the graph after some point. Therefore these tools apply the updates up to that point, output that graph in .gra format and also output the remaining updates as an update file.
    * **graphExtractorFromPairs**:
    * **graphExtractor**:

## Installation ##
A simple _make_ command in the folder would compile all the code as long as the c++ compiler supports the [C++0x](http://en.wikipedia.org/wiki/C%2B%2B0x) standard. It is because of the dependency to the [hashmap](http://en.wikipedia.org/wiki/Unordered_map_(C%2B%2B))s.

Please note that in some very large input graphs DGRAIL may give segmentation fault due to stack overflow. The reason is that in a very highly connected graph, a DFS can go very deep during the search. You can overcome this problem by setting a higher stack size.

Example: _ulimit -s 200000_

## Usage ##

### RUNNING DAGGER on a graph and update file ###
It prints the input graph statistics at the beginning and at the end of the simulation.
(e.g. #nodes, #edges, #dagnodes, size of largest SCC)
At the end it outputs the total time and particular update time averages.

Usage:
`./DGRAIL -g <grafile> -t <updatefile> -qpu <queriesperupdate> -type [mg | bmg | dfs | bfs] -dim <#dims>`

**Example DFS RUN**:

`./DGRAIL -g samplegraphs/sample2.gra -t testfiles/mytest.test -type dfs -qpu 5`
  * No index is used and queries are answered by DFS. For each update in the input graph, 5 random queries are generated and answered.

**Example BFS RUN**:

`./DGRAIL -g samplegraphs/sample2.gra -t testfiles/mytest.test -type bfs -qpu 5`
  * No index is used and queries are answered by BFS. For each update in the input graph, 5 random queries are generated and answered.

**Example DAGGER RUN with DFS Search**:

`./DGRAIL -g samplegraphs/sample2.gra -t testfiles/mytest.test -type mg -dim 1 -qpu 5`
  * DAGGER with 1 interval label per node. For each update in the input graph, 5 random queries are generated and answered. Queries are answered by pruned DFS search. If `-dim 0` is set as the dimensionality, then the index maintains no labels so it will be basically a DFS search on the DAG graph (instead of the input graph).

**Example DAGGER RUN with BFS Search**:

`./DGRAIL -g samplegraphs/sample2.gra -t testfiles/mytest.test -type bmg -dim 1 -qpu 5`
  * DAGGER with 1 interval label per node. For each update in the input graph, 5 random queries are generated and answered. Queries are answered by pruned BFS search. If -dim 0 is set as the dimensionality, then the index maintains no labels so it will be basically BFS search on the DAG graph (instead of the input graph).

If you want to process the first N lines of the update file, use -n N in the args.

### TESTING CORRECTNESS ###
You can check the correctness of the index, anytime you doubt. It has the same usage with DGRAIL. It compares the result of each query with the result of the plain DFS search. It halts with an error message if a mismatch occurs, otherwise it terminates with "Exiting..." line.

Usage:
`./correctness -g <grafile> -t <updatefile> -qpu <queriesperupdate> -type mg -dim 2`

It is better to give high `<queriesperupdate>` value if the number of updates is small...

### GRAPH GENERATION ###

We have 3 graph generators that can be used to generate million node graphs:
> `./erGenerator -n <graphsize> -d <avg_degree>       `: generates erdos-renyi

> `./baGenerator -n <graphsize> -d <avg_degree>       `   : generates albert-barabasi (DAG)

> `./baGenerator -n <graphsize> -d <avg_degree>  -bidir`  : generates albert-barabasi (nondag)

They print some messages to std err to let you know about the progress. erGenerator is fast, but baGenerator takes some time. Default value of average degree is 4. You can set it to another value with -d flag. It picks a random number from 1 to 2\*avgdeg for each node, and creates that many edges. It outputs the generated gra file in out.gra

### UPDATE FILE GENERATION ###

We need to generate test file (update file) for testing an index.

Usage:

`./testGenerator -g <grafile> -n <#updates> [-pie | -pin | -pde | -pdn]`

It creates n lines of update operations on a given graph. By default it generates a mixed workload
  * 60% : insert edge
  * 20% : insert node
  * 15% : delete edge
  * 5%  : delete node

However it is possible to generate pure workloads with the optional flags:
  * -pie : only edge insertions
  * -pin : only node insertions
  * -pde : only edge deletions
  * -pdn : only node deletions

By default, edge insertions are preferential attachment. But it can be set to ER if you give "-imode 0" to the program. The program outputs to standard output.

### GRAPH EXTRACTORS ###

  * graphExtractorFromPairs
Given a timestamp sorted edge lists (each line has 2 nodes), it creates a gra file with first n lines and convert the remaining list into "add node" updates.

Example Usage :
`./graphExtractorFromPairs -g abc.text -n 10000000`

It will create abc.text.gra with first 10M lines of _abc.text_ and the remaining lines will be converted to update operations written in _abc.text.gra.up_

  * graphExtractor
Given an update list, it creates a gra file with first n line.
Example Usage:
`./graphExtractor -g abc.text -n 1000 -t outfile.gra`

The output gra is saved into _outfile.gra_
