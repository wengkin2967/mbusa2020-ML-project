# mbusa2020-ML-project

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
#### Dataset summary
* **train.txt**: information on which two authors id shares a link, there are some incorrect edges here but its up to the model to detect them after training as theres no way we could know at this stage.
* **nodes.json**: attributes for each author (linked by author id)
* **dev.csv**, **dev-labels.csv**: together form similar dataset like train.txt, usually used for hyperparameter tuning / testing. (-1/1 to represent no edge/edge)

<b>&nbsp;</b>
#### Transformation overview
* there is no way to predict anything just from id pairs (**train.txt**) unless we let the model memorize which two author are linked but that wouldn't be a machine learning technique, a natural way to start is to create a new training set that combine this pairing information with more information (attributes from **nodes.json**), so the model can learn from this extra information instead.
* need to combine **edge information** from train.txt with **nodes information** from nodes.json to form a training set. 
    * the same transformation need to apply in testing phase
    * this requires us to have all author information in json (including the authors in test set), or new author information will be required

<b>&nbsp;</b>
#### What is the table like?    
- refer to sample-table.csv

<b>&nbsp;</b>
#### Transformation details
* labelling of whether there is an edge between two authors can be obtained from train.txt. Use 1 when there is an edge, -1 otherwise
* what features to include? (feature selection)
    1. years between first and last publish of author 1
    2. years between first and last publish of author 2
    3. difference of 3 and 4 (will this contribute to multicolinearity)
    4. number of terms shared between the two nodes 
        * (or use 1 or 0 to indicate whether both authors used a same term for each term)
    5. number of venue shared between two nodes
        * (or use 1 or 0 to indicate whether both authors published at a venue for each venue)
    6. ...
* should we link a node to itself? (does this provide information that really similar nodes should link together)
* should pairs that are not linked (edge=0) be included, or just include pairs that shares an edge (edge=1), will this help the model to learn better what type of combinations should not share an edge?

## 2. Training model
The model need to output probability of an edge being true, some models we can experiment with at the moment are (sklearn built in models):
1. naive bayes (multinomial/gaussian). 
    - gaussian is used to compute conditional probability when predictors are continuous.
2. logistic regression
3. decision trees
4. random forest (ensemble)
5. ...

<b>&nbsp;</b>
some things to be aware of:
- outputs of sklearn model, is it a probability, or just a class label that extra steps are required to find the probability.
- is the model compatible with our attribute type (this can be something to talk about in error analysis)

## 4. Evaluation

### Evaluation metrics:
1. accuracy, precision, etc..
2. confusion table
3. AUC

<b>&nbsp;</b>
#### Benchmark
- naive
- majority
- decision stump

<b>&nbsp;</b>
*And finally compare results from training set (cross validation) with test set.
