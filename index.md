---
layout: index
---


Classifier Development in R
======

This book is intended to serve as an introduction to production-level classifier
development in the R programming language. The sections denoted by \* are optional.

1. Introduction
  - [Setting up your development environment](docs/introduction/setting_up_your_development_environment.md)
  - [A review of R](docs/introduction/review_of_R.md)
  - [Exercises](docs/introduction/review_quiz.md)
  - \* [Troubleshooting](docs/introduction/troubleshooting.md)

2. [Data Preparation](docs/data_preparation/index.md)
  - [Training versus prediction](docs/data_preparation/training_versus_prediction.md)
  - Some manual exercises
    - Filtering out values
  - Transformations
    - Column transformations
    - Row transformations
    - Multi-column transformations
    - Dataframe transformations
    - Exercises 
  - Mungebits
    - A simple filter
    - A simple imputer
    - The mungebit data structure
    - The imputer mungebit
    - More advanced mungebits
    - Passing state between training and prediction
    - Exercises 
    - \*Writing our own mungebit
    - \*Testing mungebits
  - Mungepieces
    - An imputer with memory
    - Differences between training and prediction
    - Using the `parse_mungepiece` function to make mungepieces
    - Exercises
  - Munging
    - Putting it all together with `munge`
    - Re-munging against a data.frame
    - \*Inspecting stored mungepieces
    - \*Debugging the data preparation process

3. Modeling
  - Some manual exercises
    - A linear regression
    - A GBM model
  - Tundra (turning models into R objects)
    - A linear regression tundra model
    - A GBM tundra model
    - Understanding training parameters
    - Using prediction parameters
    - The philosophy of tundra
    - \* Writing our own GLM tundra container
  - Stagerunner
    - An example of the full modeling process
    - Introducing stagerunner: parametrizing our modeling process
    - Quick detour: other use cases for stagerunner
    - \* Advanced features of stagerunner objects
    - \* Debugging stagerunner objects
    - Interactive stagerunners and caching
    - An ensemble of stagerunners
  - Syberia
    - Introduction
    - The import stage
    - The data stage
    - The model stage
    - The export stage
    - \* The evaluation stage
    - Testing your syberia models
    - Re-factoring complicated models with Ramd

4. Deployment
  - Microserver
    - Launching a microserver on EC2
    - Deploying your syberia model to S3
  - Validation
    - The philosophy of validation
    - Testing alpha versus beta operations
  - Scaling with nginx

