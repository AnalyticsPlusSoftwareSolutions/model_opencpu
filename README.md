# model_opencpu
https://analyticsplussoftwaresolutions.ocpu.io/

Wyjaśnienei modelu hostowanego na opencpu

```
library(rjson)
library(DALEX)

#funkcja predict dla DALEX-a
custom_pred <- function(model, data){
  x <- toJSON(unname(split(data, 1:nrow(data))))
  (tmp <- httr::POST("https://analyticsplussoftwaresolutions.ocpu.io/model_opencpu/R/pred/json",
                     body = list(input = x)))
  result <- rawToChar(tmp$content)
  fromJSON(result)
}

#Sprawdzenie, że działa :)
df <- data.frame(group = "Trt")
custom_pred(4, df)

#Dane dle DALEX-a
ctl <- c(4.17,5.58,5.18,6.11,4.50,4.61,5.17,4.53,5.33,5.14)
trt <- c(4.81,4.17,4.41,3.59,5.87,3.83,6.03,4.89,4.32,4.69)
group <- gl(2, 10, 20, labels = c("Ctl","Trt"))
weight <- c(ctl, trt)
data <- data.frame(group = group)
y <- weight

# explainer
explainer <- explain(model = 0, predict_function = custom_pred, data = data, y = y)
mp <- model_performance(explainer)
plot(mp)
```
