<properties title="Develop a predictive solution with Azure Machine Learning" pageTitle="Develop a predictive solution with Machine Learning | Azure" description="Walkthrough of how to create a predictive analytics experiment in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye"></tags>

# Develop a predictive solution with Azure Machine Learning

Suppose you need to predict an individual's credit risk based on the information they give on a credit application.

That's a complex problem, of course, but let's simplify the parameters of the question a bit and use it as an example of how you might be able to use Microsoft Azure Machine Learning with ML Studio and ML API service to create such a predictive analytics solution.

In this walkthrough, we'll follow the process of developing a predictive analytics model in ML Studio and then publishing it to the ML API service. We'll start with publicly-available credit risk data, develop and train a predictive model based on that data, and then publish the model as a web service that can be used by others.

We'll follow these steps:

1.  [Create an ML workspace][Create an ML workspace]
2.  [Upload existing data][Upload existing data]
3.  [Create a new experiment][Create a new experiment]
4.  [Train and evaluate the models][Train and evaluate the models]
5.  [Publish the web service][Publish the web service]
6.  [Access the web service][Access the web service]

This walkthrough is based on a simplified version of the
[Credit risk prediction sample experiment][Credit risk prediction sample experiment] included with ML Studio.

  [Create an ML workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Upload existing data]: ../machine-learning-walkthrough-2-upload-data/
  [Create a new experiment]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Train and evaluate the models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publish the web service]: ../machine-learning-walkthrough-5-publish-web-service/
  [Access the web service]: ../machine-learning-walkthrough-6-access-web-service/
  [Credit risk prediction sample experiment]: ../machine-learning-sample-credit-risk-prediction/
