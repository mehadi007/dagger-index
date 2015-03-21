In _Scripts_ folder there will be 4 scripts:

  1. runMethod.sh
  1. extractTotalTime.sh
  1. qpuPlot.sh
  1. qpuPlotbw.sh
  1. example.sh

### runMethod.sh ###
`sh runMethod.sh <grafile> <updatefile>`

It runs DFS, DG0, DG1 and DG2 on the files for varying values of 1 to 8. (you can reduce the number of values for vary large graphs). It saves the outputs of DGRAIL in the folder _Scripts/depo/_ : with suffixes that specify each run.

**An example run:**

`sh runMethods.sh ../../abc.gra abc.update`

It will create the following files in the depo folder.
`abc_dfs_1 abc_dfs_2, ..., abc_dfs_20 abc_mg0_1 ... abc_mg0_20 ... abc_mg2_20`

### extractTotalTime.sh ###

`sh extractTotalTime.sh <basefilename>`

Checks the folders in _Scripts/depo/_ and creates the table to be plotted. If basefilename is _abc_, it will read the files created in the previous example and saves the table in the file _abc.tt_


### qpuPlot.sh ###

`sh qpuPlot.sh <totaltimetablefile> <maxqpu>`

Given _abc.tt_ , it produces _abc.tt.eps_ and _abc.tt.jpg_ . The second argument is the max value in x axis...
**Sample usage:** `sh qpuPlot.sh abc.tt 20`

### qpuPlotbw.sh ###

Same with qpuPlot except this one only uses black lines with different line styles.

### example.sh ###
An example of running the above scripts...