﻿README
======

Analyze Jira issues to measure teams efficiency in terms of engineering processes, structure and practices. Metrics once calculated can be later published on the Confluance page (Confluance Jira macros are supported for dynamic data refresh). It is also possible to publish data to the html file. All the setup can be done in jupyter notebooks.
The ultimate goal is to use those metrics to continuously improve efficiency and enable fast delivery

There are many tools available, such as eazyBI, but thanks to the custom code we can do everything. This approach also does not require any administrator privileges in Jira or Confluance. Just use the credentials of your regular user to access data in Jira and generate a custom dashboard on the Confluance page(s).

## Algorithms

1. __Dependency factor__ calculates the number of issues with external dependencies to the total number of issues not Done yet, but after refinement (estimated in the backlog or already planned for the sprint).
Note that you need to specify Workload & Statuses to determin which issues are Ready for Development (after refinement) 

    Issues selection in the example (notebooks/Dependency_metrics.ipynb) is based on the below JQL:

    `'project = {jira_project_id} AND issuetype not in ("Test", "Sub-Task", "Release", "Risk", "Incident") and status not in ("Done", "In Analysis", "Open")'`

    The dependency factor algorithm is following below criterias:

    - search for 'external' dependencies, which might be direct or indirect (ignore pure internal dependencies)
    - link type  == ("Is blocked by", "Depends on").
    - filter out linked dependency if status == ('Done'). 

    Available analysis:

    - Historical independency metrics ('all issues' vs 'issues with dependencies' - stacked bar chart with independency factor 0-100 on the Y axis and last 8 dates on the X axis)
    - External dependency by Squad (Jira external projects)
    - Issues with dependency by Initiative (Jira Epic in Squad project)
    - Dependency issues initiatives (Jira Epics in external projects)
    - Dependency graphs showing up to second level links
    - External but in DM vs external outside of DM
    - Total Independency metric:  sum(all_issues) / sum(all_with_dep)
    - Total independency history chart

Examples key results:
- All teams independence factor > 90% (no more than 10% stories dependent on work from other team)
- Single team independence factor > 80%

2. __Project progress__ calculates progress of the project based on number of issues done vs defined for the given epic(initiative)

    Issues selection in the example (notebooks/Project_execution_metrics.ipynb) is based on the below parameters:

    `jql = f'"Epic Link" = {epic_key}'`
    
    `status_done=('Done', 'Waiting for production')`

    Available analysis:
    - Percentage done
    - Sprint details
    - Pie chart by issue type (Confluance Jira macro)
    - Reminding work
    - Risks
    - Blocked stories (list and dependency graphs)
# Setup project

Create virtual environment
`python3 -m venv venv`

### MAC OS
Activate venv

`. ./venv/bin/activate`

`pip install -r requirements.txt `

in case of `error: Error locating graphviz` on Mac OS do following:

`brew install graphviz`

`pip install pygraphviz --install-option="--include-path=/usr/local/include/graphviz/" --install-option="--library-path=/usr/local/lib/graphviz"`

### Windows 10

`pip install -r requirements_win10.txt `

Graphviz

Step 1: Download and install Graphviz

`https://graphviz.gitlab.io/_pages/Download/Download_windows.html`

Step 2: Add below path to your PATH environment variable

`C:\Program Files (x86)\Graphviz2.38\bin`

Step 3: Re-open command line and activate venv

`venv\Scripts\activate`

Step 4: Download binaries for pygraphviz and install in active venv

`https://github.com/CristiFati/Prebuilt-Binaries/tree/master/PyGraphviz/v1.5/Graphviz-2.42.2`

For example:

In case of python 3.7

`pip install pygraphviz-1.5-cp37-cp37m-win_amd64.whl`

In case of python 3.8

`pip install pygraphviz-1.5-cp38-cp38-win_amd64.whl`

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

# Run jupyter notebook(s) to calculate and publish metrics

`jupyter notebook`


if you have notebooks setup you can execute all of them from the command line using below script

`./gen_dashboards.sh`

Go to your Confluance page to see generated dashboard(s)

# Roadmap

1. Reorganize code to make it easy to read (structure to reflect functionality not layers)

Add more engineering metrics:

2. Add more Planning and Execution metrics. Measure the amount of work done compared to work carried over to the next iteration. Unlike the Story Points, the percentage of work done compared to planned can be compared between teams. Such metrics can also be aggregated for the organisation level

Example of key results:

- 90% of sprint content committed by the team is delivered to production
- 80% of the revenue generating use cases are delivered on time
3. Add Quality metrics on different levels (organisation level, squad level, chapter level). Goal is to monitor negative impact on end user, error budget, waste factor, etc.

Example of key results:

- 15% or less Change Failure Rate (organisation level)
- 10% or less BAU is enough to make maintenance backlog stable (squad level)
- 90% or more of test cases for the Release are in the regression test suite (squd level)
- 10% or less of Releases are for SLO hardening. Error budget 99% SLO/MTTR (squad level).
- 100% tests automation for scenarios likely to happen with medium or high chance of occurance (QA chapter level)

4. Add Delivery metrics

Example of key results:

- Deployment frequency to production no less then every 3 days (aim many times a day)
- Cycle time
- Lead time for changes
