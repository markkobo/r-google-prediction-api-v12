R package implemention of Google Prediction API v1.2

Modification and some extension of 'R client library for the Google Prediction API' (Markko Ko, Google Inc., 2010) for Google Prediction API v1.2

*Deprecated together with google prediction api:*
https://cloud.google.com/prediction/docs/

Platform
Windows 
Prerequisities
R 2.13.0
Python 2.7


Google Prediction API can be accessed from IP addresses authorized in Google API Console only
you have to manually authorize GSUtil ('gsutil config') and create .boto file
Notes

Sample code

install package
```
install.packages("googlepredictionapi_0.12.tar.gz", repos=NULL, type="source")
```

library(rjson) library(RCurl) library(googlepredictionapi)

--- initialize

turn off SSL check - see: http://code.google.com/p/r-google-analytics/issues/detail?id=1#c5 & http://www.omegahat.org/RCurl/FAQ.html
```
options(RCurlOptions = list(capath = system.file("CurlSSL", "cacert.pem", package = "RCurl"), ssl.verifypeer = FALSE))
```
put your own email, password and API key below
```
myEmail <- "" myPassword <- "" myAPIkey <- "*"
```
put path to python.exe on your computer and path do gsutil directory
```
myPython <- "c:/Python27/python.exe" myGSUtilPath <- "c:/gsutil/"
myVerbose <- FALSE
```
--- work

upload local CVS file to Google Storage and initiate training; local file must be in R working directory
```
my.model <- PredictionApiTrain(data="./language_id_pl.txt",remote.file="gs://prediction_example/prediction_models/languages")
```
alternative: initiate training of a model already uploaded to Google Storage
```
my.model <- PredictionApiTrain(data="gs://prediction_example/prediction_models/languages",tillDone=FALSE) # tillDone - repeat checking till model is trained
```
check whether model is trained; if tillDone=TRUE was set above, there is no need for that
```
result <- PredictionApiCheckTrainingStatus("prediction_example","prediction_models/languages",verbose=TRUE)
```
you can adapt the result returned by PredictionApiCheckTrainingStatus to 'predictionapimodel' class used in predictions
```
my.model <- WrapModel(result)
summary(my.model)
```
check new data against model (I have added some Polish-language texts to the Google Prediction API 'Hello World' example)
```
predict(my.model,"'Prezydent Obama spotkał się z parlamentarzystami'")
```
please note, this package returns all labels and scores for a given data in a format:

[1] "Polish" "French" "Spanish" "English" "0.36195" "0.26396" "0.260067" "0.114022"

some other prediction request
```
predict(my.model,"'This is a test'")
```
list objects in a Google Storage bucket
```
PredictionApiListObjects("prediction_example","prediction_models/languages",verbose=TRUE) 
```
