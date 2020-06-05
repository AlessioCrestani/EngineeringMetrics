﻿README
======

This is the library to analyze Jira issues to measure teams efficiency in terms of engineering processes, structure and practices. Metrics once calculated can be later published on the Confluance page (Confluance Jira macros are supported for dynamic data refresh). It is also possible to publish data to the html file. All the setup can be done in jupyter notebooks.
The ultimate goal is to use those metrics to continuously improve efficiency and enable fast delivery

# Features
1. Algorithms

Dependency factor calculates the number of issues with external dependencies to the total number of issues not Done yet, but after refinement (estimated in the backlog or already planned for the sprint).
Note that you need to specify Workload & Statuses to determin which issues are read for development (after refinement)

Issues selection in the example (DAN_dependency_factor_after_refinement.ipynb) is based on the below JQL:

`'project = {jira_project_id} AND issuetype not in ("Test", "Sub-Task", "Release", "Risk", "Incident") and status not in ("Done", "In Analysis", "Open")'`

The dependency factor algorithm is following below criterias:

1. search for 'external' dependencies, which might be direct or indirect (ignore pure internal dependencies)

2. link type  == ("Is blocked by", "Depends on").

3. filter out linked dependency if status == ('Done'). 

Available analysis:

1. Historical independency metrics ('all issues' vs 'issues with dependencies' - stacked bar chart with independency factor 0-100 on the Y axis and last 6 dates on the X axis)
2. External dependency by Squad (Jira external projects)
3. Issues with dependency by Initiative (Jira Epic in Squad project)
4. Dependency issues initiatives (Jira Epics in external projects)
5. Dependency graphs showing up to second level links
6. External but in DM vs external outside of DM
7. Total Independency metric:  sum(all_issues) / sum(all_with_dep)
8. Total independency history chart

# Setup project
`python3 -m venv venv`
`. ./venv/bin/activate`

`pip install -r requirements.txt `

if case of `error: Error locating graphviz` on Mac OS than you need to do following steps:

`brew install graphviz`

`pip install pygraphviz --install-option="--include-path=/usr/local/include/graphviz/" --install-option="--library-path=/usr/local/lib/graphviz"`
 
# Environment settings

Please remember to define following parameters in your ENV variables, example:

vim ~/.bash_profile  

```
export JIRA_USERNAME=lswolkien
export JIRA_PASSWORD=TopS3cr3t
export JIRA_URL=https://jira.mycompany.com/
export CONFLUENCE_USERNAME=lswolkien
export CONFLUENCE_PASSWORD=TopS3cr3t
export CONFLUENCE_URL=https://conf.mycompany.com/
```
Note! There are better ways of managing secrets. Use above example on your own risk!

## run jupyter to create notebooks to define metrics

`jupyter notebook`


## if you have notebooks setup you can execute all of them from the command line using below script

`./gen_dashboards.sh`

Go to your Confluance page to see generated dashboard


