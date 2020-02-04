# sankey
Build a Sankey plot from an input TSV file.

## 1) translate input TSV into JSON format

### Example TSV
```bash
12	root	Viruses	Caudovirales	Siphoviridae	Fromanvirus	unclassified Fromanvirus	Mycobacterium phage Naca
10	root	Viruses	Caudovirales	Siphoviridae	Dismasvirus	unclassified Dismasvirus	Microbacterium phage Didgeridoo
8	root	Viruses	unclassified    Baculoviridae	Betabaculovirus	unclassified Betabaculovirus	Spodoptera litura granulovirus
```
__Important__: At the moment your TSV needs to have the same amount of columns for each row. Insert _unclassified XXX_ if you are missing a rank.

Here, we have identified 12 Mycobacterium phage Naca, 10 Microbacterium phage Didgeridoo, and 8 litura granulovirus. We will use a script to build the JSON format that looks like this:

### Example JSON
```json
{"nodes":[
    {"name":"Viruses","id":0},
    {"name":"Caudovirales","id":1},
    {"name":"Siphoviridae","id":2},
    {"name":"Fromanvirus","id":3},
    {"name":"unclassified Fromanvirus","id":4},
    {"name":"Mycobacterium phage Naca","id":5},
    {"name":"Dismasvirus","id":6},
    {"name":"unclassified Dismasvirus","id":7},
    {"name":"Microbacterium phage Didgeridoo","id":8},
    {"name":"unclassified","id":9},
    {"name":"Baculoviridae","id":10},
    {"name":"Betabaculovirus","id":11},
    {"name":"Spodoptera","id":12},
    {"name":"litura granulovirus","id":13}
    ],
    "links":[
    {"source":0,"target":1,"value":22},
    {"source":0,"target":9,"value":8},
    {"source":1,"target":2,"value":22},
    {"source":2,"target":3,"value":12},
    {"source":3,"target":4,"value":12},
    {"source":4,"target":5,"value":12},
    {"source":2,"target":6,"value":10},
    {"source":6,"target":7,"value":10},
    {"source":7,"target":8,"value":10},
    {"source":9,"target":10,"value":8},
    {"source":10,"target":11,"value":8},
    {"source":11,"target":12,"value":8},
    {"source":12,"target":13,"value":8}
    ]}
```

Run:
```bash
./csv2json.rb test/viruses.csv
```

The resulting ``.json`` file can be used to plot the Sankey. 

## 2) Sankey plot

Based on [https://github.com/fbreitwieser/sankeyD3](https://github.com/fbreitwieser/sankeyD3).

### Install R and dependencies
```bash
conda create -n sankey -c r r-base pandoc
conda activate sankey
R
```
```R
# basics needed for Sankey HTML
install.packages('devtools')
devtools::install_github("fbreitwieser/sankeyD3")
```

# Generate Sankey via interactive R session
```bash
R
```
```R
library(sankeyD3)
library(magrittr)

Taxonomy <- jsonlite::fromJSON("test/viruses.csv.json")

# show in browser
sankeyNetwork(Links = Taxonomy$links, Nodes = Taxonomy$nodes, Source = "source", Target = "target", Value = "value", NodeID = "name", units = "count", fontSize = 12, nodeWidth = 30, nodeShadow = TRUE, nodePadding = 20, nodeStrokeWidth = 1, nodeCornerRadius = 10, dragY = TRUE, dragX = TRUE, numberFormat = ",.3g") 

# print to HTML file
sankeyNetwork(Links = Taxonomy$links, Nodes = Taxonomy$nodes, Source = "source", Target = "target", Value = "value", NodeID = "name", units = "count", fontSize = 12, nodeWidth = 30, nodeShadow = TRUE, nodePadding = 20, nodeStrokeWidth = 1, nodeCornerRadius = 10, dragY = TRUE, dragX = TRUE, numberFormat = ",.3g") %>% saveNetwork(file = 'viruses_sankey.html')

```
