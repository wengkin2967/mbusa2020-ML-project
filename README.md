# mbusa2020-ML-project

## Overview

<b>&nbsp;</b>
The main steps for this project is as follow, some details are discussed in following cells:

1. data preprocessing/transformation

2. create models and train with **training set**

3. for each model:  
   3.1 hyperparameter tuning with **dev set**
   3.2 obtain accuracy from **dev set**

4. model selection:  
   4.1 select model with best accuracy from **3.2**
   4.2 error analysis - compare and explain different models

5. make predictions for test set to be submitted:  
   * **Option 1** (mentioned in the project specs)  
     5.1 use model from 4.1 to make predictions on test set and submit  
  
   * **Option 2** (could give us more advantage on kaggle)  
     5.1 use model from 4.1 with the tuned hyperparameter, retrain on full dataset "train + dev"  
           (this way the model can learn from more data)  
     5.2 make predictions on test set and submit
     https://stats.stackexchange.com/questions/11602/training-on-the-full-dataset-after-cross-validation
     https://www.kaggle.com/general/9831

6. Report writting


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

  11. *keyword_similarity*: number of common keywords / all (union) keywords of two author

  12. *common_venue*: number of venue shared between two nodes    

  13. *venue_similarity*: number of common venues / all (union) venues of two author

  14. *common_neighbours*: number of neighbours shared between two nodes

  15. *neighbours_similarity*: number of common neighbours / all (union) neighbours of two authors

  * class label: *edge* - 1 if src and sink share an edge, 0 otherwise



## Models:

1. naive bayes (multinomial/gaussian). 
   - gaussian is used to compute conditional probability when predictors are continuous.
2. logistic regression
3. decision trees
4. random forest (ensemble)
5. ...


some things to be aware of:

- outputs of sklearn model, is it a probability, or just a class label that extra steps are required to find the probability.
- is the model compatible with our attribute type (this can be something to talk about in error analysis)

## Evaluation

### Evaluation metrics:

1. accuracy, precision, etc..
2. confusion table
3. AUC

### Benchmark:

- naive
- majority
- decision stump
