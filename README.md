<!-- README.md is generated from README.Rmd. Please edit that file -->
Why Use WOE Analysis?
=====================

Binary classification models are perhaps the most common use-case in predictive analytics. The reason is that many key client actions across a wide range of industries are binary in nature, such as defaulting on a loan, clicking on an ad, or terminating a subscription.

Prior to building a binary classification model, a common step is to perform variable screening and exploratory data analysis. This is the step where we get to know the data and weed out variables that are either ill-conditioned or simply contain no information that will help us predict the action of interest. Note that the purpose of this step should not to be confused with that of multiple-variable selection techniques, such as stepwise regression, where the variables that go into the final model are selected. Rather, this is a precursory step designed to ensure that the approaches deployed during the final modeling phases are set up for success.

The *weight of evidence* (WOE) and *information value* (IV) provide a great framework for for exploratory analysis and variable screening for binary classifiers. WOE and IV have been used extensively in the credit risk world for several decades, and the underlying theory dates back to the 1950s. However, it is still not widely used in other industries.

WOE and IV analysis enable one to:

-   Consider each variable’s independent contribution to the outcome.
-   Detect linear and non-linear relationships.
-   Rank variables in terms of "univariate" predictive strength.
-   Visualize the correlations between the predictive variables and the binary outcome.
-   Seamlessly compare the strength of continuous and categorical variables without creating dummy variables.
-   Seamlessly handle missing values without imputation.
-   Assess the predictive power of missing values.

About the Information Package
=============================

The `Information` package is designed to perform WOE and IV analysis for binary classification models as well as uplift models. To maximize performance, aggregations are done in `data.table`, and creation of WOE vectors can be distributed across multiple cores.

Extensions to Exploratory Analysis for Uplift Models
====================================================

Consider a direct marketing program where a *test group* received an offer of some sort, and the *control group* did not receive anything. The test and control groups are based on a random split. The lift of the campaign is defined as the difference in success rates between the test and control groups. In other words, the program can only be deemed successful if the offer outperforms the "do nothing" (a.k.a baseline) scenario.

The purpose of uplift models is to estimate the difference between the test and control groups, and then using the resulting model to target *persuadables* – i.e., potential or existing clients that are on the fence and need some sort of offer or contract to sign up or buy a product. Thus, when preparing to build an uplift model, we cannot only focus on the log odds of \(Y=1\) (where \(Y\) is some binary outcome), we need to analyze the *log odds ratio* of \(Y=1\) for the test group versus the control group. This can be handled by the *net weight of evidence* (NWOE) and the *net information value* (NIV).

Simple Example
==============

``` r
library(Information)

# Basic WOE and IV -- no external cross validation
# Set ncore=2 since CRAN does now allow more than 2 for examples
# For real applications, leave ncore is NULL to get the default which is: number of cores - 1
data(train, package="Information")
train <- subset(train, TREATMENT==1)
IV <- CreateInfoTables(data=train, y="PURCHASE", ncore=2)

# Show the first records of the IV summary table
print(head(IV$Summary), row.names=FALSE)
```

    ##                     Variable        IV
    ##              N_OPEN_REV_ACTS 1.0107695
    ##         TOT_HI_CRDT_CRDT_LMT 0.9345902
    ##         RATIO_BAL_TO_HI_CRDT 0.8232539
    ##  D_NA_M_SNC_MST_RCNT_ACT_OPN 0.6355466
    ##   M_SNC_OLDST_RETAIL_ACT_OPN 0.5573438
    ##       M_SNC_MST_RCNT_ACT_OPN 0.5026402

``` r
# Show the WOE table for the variable called N_OPEN_REV_ACTS
print(IV$Tables$N_OPEN_REV_ACTS, row.names=FALSE)
```

    ##  N_OPEN_REV_ACTS    N    Percent        WOE        IV
    ##            [0,0] 1469 0.29545455 -2.0465968 0.6401443
    ##            [1,2]  958 0.19267900 -0.5900120 0.6958705
    ##            [3,3]  310 0.06234916  0.2033085 0.6986029
    ##            [4,5]  583 0.11725664  0.4419768 0.7244762
    ##            [6,8]  632 0.12711183  0.6148243 0.7810611
    ##           [9,11]  453 0.09111022  0.8815772 0.8692672
    ##          [12,48]  567 0.11403862  0.9883818 1.0107695

How to Install
==============

You can install:

-   The latest development version from github with

``` r
devtools::lnstall_github("klarsen1/Information", "klarsen1")
```

-   The latest released version from CRAN with

``` r
install.packages("Information")
```
