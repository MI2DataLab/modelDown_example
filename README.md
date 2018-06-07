# modelDown ShowCase

Examples for modelDown

## Regression for apartments data

A use case for 4 different regression model with data with 5 variables, 4 numerical and 1 categorical.

Here is the script:

```{R}
library("randomForest")
library("breakDown")
library("DALEX")
library("gbm")
library("e1071")

# lm

apartments_lm_model <- lm(m2.price ~ construction.year + surface + floor + 
                         no.rooms + district, data = apartments)
explainer_lm <- explain(apartments_lm_model, 
                          data = apartmentsTest[,2:6], y = apartmentsTest$m2.price)

# randomForest
set.seed(59)
apartments_rf_model <- randomForest(m2.price ~ construction.year + surface + floor + 
                      no.rooms + district, data = apartments)
explainer_rf <- explain(apartments_rf_model, 
                       data = apartmentsTest[,2:6], y = apartmentsTest$m2.price)

# gbm
apartments_gbm_model <- gbm(m2.price ~ construction.year + surface + floor + 
                         no.rooms + district, data = apartments, n.trees = 1000)
explainer_gbm <- explain(apartments_gbm_model, 
                          data = apartmentsTest[,2:6], y = apartmentsTest$m2.price,
                         predict_function = function(m, d) predict(m, d, n.trees = 1000))

# SVM
apartments_svm_model <- svm(m2.price ~ construction.year + surface + floor + 
                         no.rooms + district, data = apartments)

explainer_svm <- explain(apartments_svm_model, 
                          data = apartmentsTest[,2:6], y = apartmentsTest$m2.price)


# Website generation
modelDown(explainer_lm, explainer_rf, 
          explainer_gbm, explainer_svm,
  modules = c("model_performance", "variable_importance", 
              "variable_response", "prediction_breakdown"),
  output_folder = "modelDown_example",
  pb.observations = c(161, 731, 2741, 4454),
  vr.type = "pdp",
  plot_width = 800,
  pb.plot_width = 800,
  mp.plot_width = 800,
  vi.plot_width = 800,
  vr.plot_width = 800)
```
