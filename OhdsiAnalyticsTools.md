# OHDSI Analytics Tools {#OhdsiAnalyticsTools}

*Chapter lead: Martijn Schuemie*

OHDSI offers a wide range of open source tools to support the various data-analytics use cases. What these tools have in common is that they can all interaction with one or more databases using the Commond Data Model (CDM). Furthermore, these tools standardize the analytics for various use cases; Rather than having to start from scratch, an analysis can be implemented by filling in standard templates. This makes performign analysis easier, and also improves reproducibility and transparancy. For example, there appear to be a near-infinte number of ways to compute an incidence rate, but these can be specified in the OHDSI tools with a few choices, and anyone making those same choices will compute incidence rates the same way. 

In this chapter we first describe various ways in which we can choose to implement an analysis, and what strategies the analysis can employ. We then review the various OHDSI tools and how they fit the various use cases.

## Analysis implementation

Figure \@ref(fig:implementations) shows the various ways in which we can choose to implement a study against a database using the CDM. 

<div class="figure" style="text-align: center">
<img src="images/OhdsiAnalyticsTools/implementations.png" alt="Different ways to implement an analysis against data in the CDM." width="90%" />
<p class="caption">(\#fig:implementations)Different ways to implement an analysis against data in the CDM.</p>
</div>

We may choose to write our analysis as custom code, and not make use of any of the tools OHDSI has to offer. One could write a de novo analysis in R, SAS, or any other language. This provides the maximum flexibility, and may in fact be the only option if the specific analysis is not supported by any of our tools. However, this path requires a lot of technical skill, time, and effort, and as the analysis increases in complexity it becomes harder to avoid errors in the code.

An alternative is to develop the analysis in R, and make use of the packages in the [OHDSI Methods Library](https://ohdsi.github.io/MethodsLibrary/). At a minimum, one could use the [SqlRender](https://ohdsi.github.io/SqlRender/) and [DatabaseConnector](https://ohdsi.github.io/DatabaseConnector/) packages described in more detail in Chapter \@ref(SqlAndR) that allow the same code to be executed on various database platforms, such as PostgreSQL, SQL Server, and Oracle. Other packages such as [CohortMethod](https://ohdsi.github.io/CohortMethod/) and [PatientLevelPrediction](https://ohdsi.github.io/PatientLevelPrediction/) offer R functions for advanced analytics against the CDM that can be called on in one's code. This still requires a lot of tecnhical expertise, but by re-using the validated components of the Methods Library we can be more efficient and error-free than when using completely custom code.

The third approach relies on our interactive analysis platform [ATLAS](https://github.com/OHDSI/Atlas/wiki), a web-based tool that allows non-programmers to perform a wide range of analyses efficiently. The downside is that some options may not be available.

ATLAS and the Methods Library are not independent. Some of the more complicated analytics that can be invoked in ATLAS are executed through calls to the packages in the Methods Library. Similarly, cohorts used in the Methods Library are often designed in ATLAS.

## Analysis strategy

More or less independently of how we choose to implement our analysis is the strategy that our analytics takes in answering specific questions. Figure \@ref(fig:strategies) highlights three strategies that are employed in OHDSI.

<div class="figure" style="text-align: center">
<img src="images/OhdsiAnalyticsTools/strategies.png" alt="Strategies for generating evidence for (clinical) questions." width="90%" />
<p class="caption">(\#fig:strategies)Strategies for generating evidence for (clinical) questions.</p>
</div>

The first strategy views every analysis as a single indivual study. The analysis must be pre-specified in a protocol, implemented as code, executed against the data, after which the result can be compiled and interpreted. For every question, all steps must be repeated. An example of such an analysis is the OHDSI study into the risk of angioedema associated with levetiracetam compared with phenytoin. [@duke_2017] Here, a protocol was first written, analysis code using the OHDSI Methods Library was developed and executed across the OHDSI network, and results were compiled and disseminated in a journal publication.

The second strategy develops some app that allows users to answer a specific class of questions in real time or near-real time. Once the app has been developed, users can interactively define queries, submit them, and view the results. An example is the cohort definition and generation tool in ATLAS. This tool allows users to specify cohort definitions of arbitrary complexity, and execute the definition against a database to see how many people meet the various inclusion and exclusion criteria. 

The third strategy similarly focuses on a class of questions, but then attempts to exhaustively generate all the evidence for the questions within the class. Users can then explore the evidence as needed, usually through some viewer app. One example is the OHDSI study into the effects of depression treatments [@schuemie_2018b]. In this study all depression treatments are compared for a large set of outcomes of interest across four large observational databases. The full set of results, including 17,718 empirically calibrated hazard ratios along with extensive study diagnostics, is available in an interactive web app [^systematicEvidenceUrl].

[^systematicEvidenceUrl]: http://data.ohdsi.org/SystematicEvidence/

## Re-usable artifacts

concept (live in domains) -> concept set -> cohort definition -> analysis

Aim to use Standard concepts, although tools allow using source codes

## ATLAS

ATLAS is a web-based tool that must run on a server with access to the patient-level data in the CDM. To directly run the analyses against the data, ATLAS must therefore be installed behind your organization's firewall. However, there is also a public ATLAS [^atlasUrl], and although this ATLAS instance only has access to a small simulated dataset, it can still be used for many purposes. For example, it is possible to fully define an effect estimation of prediction study in the public ATLAS, and automatically generate the R code for executing the study. 

[^atlasUrl]: http://www.ohdsi.org/web/atlas


<div class="figure" style="text-align: center">
<img src="images/OhdsiAnalyticsTools/atlas.png" alt="ATLAS user interface." width="100%" />
<p class="caption">(\#fig:atlas)ATLAS user interface.</p>
</div>

A screenshot of ATLAS is provided in Figure \@ref(fig:atlas). On the left is a navigation bar showing the various functions provided by ATLAS:

Data Sources
: Data sources provides the capability review descriptive, standardized reporting for each of the data sources that you have configured within your Atlas platform. This feature uses the large-scale analytics strategy: all descriptives have been pre-computed. Data sources is discussed in Chapter \@ref(Characterization).

Vocabulary Search
: Atlas provides the ability to search and explore the OMOP standardized vocabulary to understand what concepts exist within those vocabularies and how to apply those concepts in your standardized analysis against your data sources. This feature is discussed in Chapter \@ref(StandardizedVocabularies).

Concept Sets
: Concept sets is the ability to create your own lists of codes that you are going to use throughout your standardized analyses so by searching the vocabulary and identifying the sets of terms that you're interested in you can save those and reuse them in all of your analyses.

Cohort Definitions
: Cohort definitions is the ability to construct a set of persons who satisfy one or more criteria for a duration of time and these cohorts can then serve as the basis of inputs for all of your subsequent analyses. This feature is discussed in Chapter \@ref(Cohorts).

Characterizations
: Characterisations is an analytic capability that allows you to look at one or more cohorts that you've defined and to summarize characteristics about those patient populations. This feature uses the real-time query strategy, and is discussed in Chapter \@ref(Characterization).

Cohort Pathways
: Cohort pathways is an analytic tool that allows you to look at the sequence of clinical events that occur within one or more populations. This feature uses the real-time query strategy, and is discussed in Chapter \@ref(Characterization).

Incidence Rates
: Incidence rates is a tool that allows you to estimate the incidence of outcomes within target populations of interest. This feature uses the real-time query strategy, and is discussed in Chapter \@ref(Characterization).

Profiles
: Profiles is a tool that allows you to explore an individual patients longitudinal observational data to summarize what is going on within a given individual. This feature uses the real-time query strategy.

Population Level Estimation
: Estimation is a capability to allow you to conduct population level effect estimation studies using a comparative cohort design whereby comparisons between one or more target and comparator cohorts can be explored for a series of outcomes. This feature can be said to implement the real-time query strategy, as no coding is required, and is discussed in Chapter \@ref(PopulationLevelEstimation).

Patient Level Prediction
: Prediction is a capability to allow you to apply machine learning algorithms to conduct patient level prediction analyses whereby you can predict an outcome within any given target exposures. This feature can be said to implement the real-time query strategy, as no coding is required, and is discussed in Chapter \@ref(PatientLevelPrediction).

Jobs
: Select the "jobs" menu item to explore jobs that are running in the background for long running processes such as generating a cohort or computing cohort reports. 

Configuration
: Select the "configuration" menu item to review the data sources that have been configured in the source configuration section. 

Feedback
: This will take you to the issue log for Atlas so that you can log a new issue or to search through existing issues. If you have ideas for new features or enhancements, this is also a place note these for the development community.

## Artifact management

## Security


### How to install



## Methods Library

The [OHDSI Methods Library](https://ohdsi.github.io/MethodsLibrary/) is the collection of R packages show in  Figure \@ref(fig:methodsLibrary).

<div class="figure" style="text-align: center">
<img src="images/OhdsiAnalyticsTools/methodsLibrary.png" alt="Packages in the OHDSI Methods Library." width="100%" />
<p class="caption">(\#fig:methodsLibrary)Packages in the OHDSI Methods Library.</p>
</div>

Software validity discussed in...


### How to install

## Integrated solutions

BroadSea?





Best practices enforced in all OHDSI methods.

Ethical consideration: e.g. should always communicate uncertainty. Prespecification of research questions, etc. 



Case study: Perhaps on how to install the tools?