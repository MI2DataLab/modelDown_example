# modelDown ShowCase

Examples for [modelDown](https://github.com/MI2DataLab/modelDown)

## Classification for RMS Titanic data

A use case for 4 different classification models with 7 features: 4 numerical and 3 categorical.

Find the website here: https://mi2datalab.github.io/modelDown_example/

Here is the script:

```{R}
# Prepare the data
library("DALEX")
titanic <- na.omit(titanic)

# Random Forest model
library("randomForest")
model_titanic_rf <- randomForest(survived == "yes" ~ gender + age + class + embarked +
                                   fare + sibsp + parch,  data = titanic[,-5])
explain_titanic_rf <- explain(model_titanic_rf, 
                              data = titanic[,-c(9,5)],
                              y = titanic$survived == "yes", 
                              label = "Random Forest v7")

# SVM model
library("e1071")
model_titanic_svm <- svm(survived == "yes" ~ class + gender + age + sibsp +
                           parch + fare + embarked, data = titanic[,-5], 
                         type = "C-classification", probability = TRUE)
explain_titanic_svm <- explain(model_titanic_svm, data = titanic[,-c(9,5)], 
                               y = titanic$survived == "yes", 
                               label = "Support Vector Machines")

# GBM model
library("gbm")
model_titanic_gbm <- gbm(survived == "yes" ~ class + gender + age + sibsp +
                           parch + fare + embarked, data = titanic[,-5], n.trees = 15000)
explain_titanic_gbm <- explain(model_titanic_gbm, data = titanic[,-c(9,5)], 
                               y = titanic$survived == "yes", 
                               predict_function = function(m,x) predict(m, x, n.trees = 15000, type = "response"),
                               label = "Generalized Boosted Models")

# k-NN model

library("caret")
model_titanic_knn <- knn3(survived == "yes" ~ class + gender + age + sibsp +
                            parch + fare + embarked, data = titanic[,-5], k = 5)
explain_titanic_knn <- explain(model_titanic_knn, data = titanic[,-c(9,5)], 
                               y = titanic$survived == "yes", 
                               predict_function = function(m,x) predict(m, x)[,2],
                               label = "k-Nearest Neighbours")


# Website generation
modelDown(explain_titanic_rf, explain_titanic_gbm, 
          explain_titanic_svm, explain_titanic_knn,
  device = "svg",
  remote_repository_path = "MI2DataLab/modelDown_example/docs",
  output_folder = "modelDown_Titanic_example")

```
