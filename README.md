# mbusa2020-ML-project
### Folder structure: The src folder contains all code and datasets.
<b>&nbsp;</b>

## Overview
<b>&nbsp;</b>
The main steps for this project is as follow, some details are discussed in following cells:

1. data preprocessing/transformation
2. create models and train with **training set**
3. hyperparameter tuning with **dev set**
4. evaluation
    - select evaluation metrics
    - select benchmark models
    - use **cross evaluation** as there is no seperate test data given
    - if cross evaluation is too slow, try hold out.
5. error analysis / model selection
    - compare accuracy between models, explain the results and select a best model.

## 1. Preprocessing
Dataset summary

* **train.txt**: information on which two authors id shares a link, there are some incorrect edges here but its up to the model to detect them after training as theres no way we could know at this stage.
* **nodes.json**: attributes for each author (linked by author id)
* **dev.csv**, **dev-labels.csv**: together form similar dataset like train.txt, usually used for hyperparameter tuning / testing. (-1/1 to represent no edge/edge)

#### Transformation overview
* there is no way to predict anything just from id pairs (**train.txt**) unless we let the model memorize which two author are linked but that wouldn't be a machine learning technique, a natural way to start is to create a new training set that combine this pairing information with more information (attributes from **nodes.json**), so the model can learn from this extra information instead.
* need to combine **edge information** from train.txt with **nodes information** from nodes.json to form a training set. 
    * the same transformation need to apply in testing phase
    * this requires us to have all author information in json (including the authors in test set), or new author information will be required
* *train.txt* provides information on connected authors, but it also tells us which pairs are not connected, this information should also be included so the model can also learn features of unconnected pairs.
* This introduces a problem: **Unbalanced class** - There is a large number of source sink pairs that doesn't share a link, this would introduce bias in our models. A way to fix this is to perform **undersampling**, that is only include the same number of unconnected src-sink pairs as those that does. (refer to **stratified sampling**)


#### Transformation details
* labelling of whether there is an edge between two authors can be obtained from train.txt. Use 1 when there is an edge, -1 otherwise

* what features to include? (feature selection)

    1. *npaper_src*: number of paper published by author 1

    2. *npaper_sink*: number of paper published by author 2

    3. *src_first*: year since first publish author 1

    4. *sink_first*: year since first publish author 2

    5. *src_last*: year since last publish author 1 

    6. *sink_last*: year since last publish author 2 

    7. *first_diff* : difference of years since first publish of two users

    8. *last_diff*: difference of years since first publish of two users

    9. *overlap_years*: number of overlap years between the author publishing period

    10. *common_keywords*: number of terms shared between the two nodes 

    11. *keyword_similarity*: number of common keywords / all keywords of two author

    12. *common_venue*: number of venue shared between two nodes    

    13. *venue_similarity*: number of common venues / all venues of two author

       

* label: *edge* - 1 if src and sink share an edge, 0 otherwise

* should we link a node to itself? (does this provide information that really similar nodes should link together)

* refer to reconstructed-table.csv

## 2. Training model
The model need to output probability of an edge being true, some models we can experiment with at the moment are (sklearn built in models):
1. naive bayes (multinomial/gaussian). 
    - gaussian is used to compute conditional probability when predictors are continuous.
2. logistic regression
3. decision trees
4. random forest (ensemble)
5. ...


some things to be aware of:

- outputs of sklearn model, is it a probability, or just a class label that extra steps are required to find the probability.
- is the model compatible with our attribute type (this can be something to talk about in error analysis)

## 4. Evaluation

### Evaluation metrics:
1. accuracy, precision, etc..
2. confusion table
3. AUC

### Benchmark:

- naive
- majority
- decision stump

<b>&nbsp;</b>
*And finally compare results from training set (cross validation) with test set.