---
title: Kurátori környezetek
titleSuffix: Azure Machine Learning
description: A Azure Machine Learningban elérhető kurátori környezetek gyűjteménye
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 72ccf2a765f50358635e4a803ed0b92e60bd7d19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012212"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning kurátori környezetek

Ez a cikk a Azure Machine Learning, valamint a bennük előre telepített csomagokat és csatornákat sorolja fel. A Azure Machine Learning által biztosított, és a munkaterületen alapértelmezés szerint elérhetővé tett környezetek. Ezek a gyorsítótárban lévő Docker-rendszerképekből származnak, ami csökkenti a Futtatás előkészítési költségeit, és lehetővé teszi a gyorsabb üzembe helyezési időt. Ezekkel a környezetekkel gyorsan megkezdheti a különböző gépi tanulási keretrendszerek használatát.

> [!NOTE]
> Ez a lista a 2020. júniusi változatban frissül. Használja a Python SDK-t a legfrissebb lista beszerzéséhez. További információ a [környezetekről szóló cikkben](./how-to-use-environments.md#use-a-curated-environment)található.

## <a name="azure-automl"></a>Azure-AutoML

- [AzureML AutoML](#azureml-automl)
- [AzureML AutoML GPU](#azureml-automl-gpu)
- [AzureML AutoML DNN](#azureml-automl-dnn)
- [AzureML AutoML DNN GPU](#azureml-automl-dnn-gpu)
- [Azure AutoML DNN-jövőkép GPU](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AzureML AutoML

csomagok csatornái:
- anaconda
- Conda – hamisítás
- pytorch

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml – értelmezés = = 1.8.0
  - azureml-magyarázza-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - következtetés – séma
  - pyarrow = = 0.17.0
  - cpuinfo = = 5.0.0
- NumPy>= 1.16.0, <= 1.16.2
- Pandák>= 0.21.0, <= 0.23.4
- xgboost<= 0,90
- fbprophet = = 0,5
- setuptools – git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>AzureML AutoML GPU

csomagok csatornái:
- anaconda
- Conda – hamisítás
- pytorch

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml – értelmezés = = 1.8.0
  - azureml-magyarázza-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - következtetés – séma
  - pyarrow = = 0.17.0
  - cpuinfo = = 5.0.0
- NumPy>= 1.16.0, <= 1.16.2
- Pandák>= 0.21.0, <= 0.23.4
- fbprophet = = 0,5
- setuptools – git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>AzureML AutoML DNN

csomagok csatornái:
- anaconda
- Conda – hamisítás
- pytorch

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml – értelmezés = = 1.8.0
  - azureml-magyarázza-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - következtetés – séma
  - pytorch – Transformers = = 1.0.0
  - Távtartó = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - cpuinfo = = 5.0.0
- Pandák>= 0.21.0, <= 0.23.4
- NumPy>= 1.16.0, <= 1.16.2
- xgboost<= 0,90
- fbprophet = = 0,5
- setuptools – git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>AzureML AutoML DNN GPU

csomagok csatornái:
- anaconda
- Conda – hamisítás
- pytorch

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml – értelmezés = = 1.8.0
  - azureml-magyarázza-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - következtetés – séma
  - horovod = = 0.19.4
  - pytorch – Transformers = = 1.0.0
  - Távtartó = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - cpuinfo = = 5.0.0
- Pandák>= 0.21.0, <= 0.23.4
- NumPy>= 1.16.0, <= 1.16.2
- fbprophet = = 0,5
- setuptools – git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>AzureML AutoML DNN-jövőkép GPU

függőségek
- Python = 3.7
- pip
  - azureml-automl-Core = = 1.8.0
  - azureml-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml – értelmezés = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-magyarázza-Model = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - azureml-Train-automl = = 1.8.0
  - azureml – adathalmaz = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-pipeline-Steps = = 1.8.0
  - azureml – folyamat = = 1.8.0
  - azureml-Train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-automl-DNN-vízió = = 1.8.0

## <a name="azure-ml-designer"></a>Azure ML Designer

- [AzureML Designer](#azureml-designer)
- [AzureML Designer CV](#azureml-designer-cv)
- [AzureML Designer CV átalakító](#azureml-designer-cv-transform)
- [AzureML Designer IO](#azureml-designer-io)
- [AzureML Designer NLP](#azureml-designer-nlp)
- [AzureML Designer PyTorch](#azureml-designer-pytorch)
- [AzureML Designer PyTorch Train](#azureml-designer-pytorch-train)
- [AzureML Designer R](#azureml-designer-r)
- [AzureML Designer ajánló](#azureml-designer-recommender)
- [AzureML Designer pontszáma](#azureml-designer-score)
- [AzureML Designer átalakítása](#azureml-designer-transform)

### <a name="azureml-designer"></a>AzureML Designer

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.8
- scikit – meglepetés = 1.0.6
- pip
  - azureml-Designer-klasszikus-modulok = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Távtartó = = 2.1.7

### <a name="azureml-designer-cv"></a>AzureML Designer CV

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-CV-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>AzureML Designer CV átalakító

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-CV-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>AzureML Designer IO

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-adatelőkészítés>= 1,6
  - azureml-Designer-dataio-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>AzureML Designer NLP

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-klasszikus-modulok = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Távtartó = = 2.1.7

### <a name="azureml-designer-pytorch"></a>AzureML Designer PyTorch

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>AzureML Designer PyTorch Train

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-r"></a>AzureML Designer R

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.8
- r-kalap = 6.0
- r-catools = 1.17.1
- r-cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-Forcats = 0.5.0
- r-előrejelzés = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- r-Matrix = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nlme = 3.1
- r-a = 7,3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-reticulate = 1,12
- r-rocr = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-sztring = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-Tseries = 0,10
- r = 3.5.1
- pip
  - azureml-Designer-klasszikus-modulok = = 0.0.124

### <a name="azureml-designer-recommender"></a>AzureML Designer ajánló

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-ajánló-modulok = = 0.0.5

### <a name="azureml-designer-score"></a>AzureML Designer pontszáma

csomagok csatornái:
- alapértelmezések

függőségek
- Conda
- Python = 3.6.8
- pip
  - azureml-Designer-score-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>AzureML Designer átalakítása

csomagok csatornái:
- alapértelmezések

függőségek
- Python = 3.6.8
- pip
  - azureml-Designer-DataTransform-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML HyperDrive ForecastDNN

függőségek
- Python = 3.7
- pip
  - azureml-Core = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - azureml-automl-DNN-előrejelzés = = 1.8.0

## <a name="azureml-minimal"></a>AzureML minimális

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0

## <a name="azureml-sidecar"></a>AzureML oldalkocsi

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2

## <a name="azureml-tutorial"></a>AzureML-oktatóanyag

csomagok csatornái:
- anaconda
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml – widgetek = = 1.8.0
  - azureml – folyamat – mag = = 1.8.0
  - azureml-pipeline-Steps = = 1.8.0
  - azureml-opendatasets = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1  
  - azureml-Train-automl = = 1.8.0
  - azureml-Train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml – értelmezés = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-mlflow = = 1.8.0
  - mlflow
  - sklearn – pandák
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>AzureML VowpalWabbit 8.8.0

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-adatelőkészítés [biztosíték, pandák]

## <a name="dask"></a>Dask

- [AzureML Dask CPU](#azureml-dask-cpu)
- [AzureML Dask GPU](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>AzureML Dask CPU

csomagok csatornái:
- Conda – hamisítás
- pytorch
- alapértelmezések

függőségek
- Python = 3.6.9
- pip
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - dask [teljes]
  - dask – ml [kész]
  - elosztott
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notebook
  - pyarrow

### <a name="azureml-dask-gpu"></a>AzureML Dask GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.9
- pip
  - azureml – alapértékek = = 1.8.0
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - dask [teljes]
  - dask – ml [kész]
  - elosztott
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notebook
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [AzureML Chainer 5.1.0 CPU](#azureml-chainer-510-cpu)
- [AzureML Chainer 5.1.0 GPU](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>AzureML Chainer 5.1.0 CPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - láncolás = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>AzureML Chainer 5.1.0 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - láncolás = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0,15

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [AzureML PyTorch 1,0 processzor](#azureml-pytorch-10-cpu)
- [AzureML PyTorch 1,0 GPU](#azureml-pytorch-10-gpu)
- [AzureML PyTorch 1,1 processzor](#azureml-pytorch-11-cpu)
- [AzureML PyTorch 1,1 GPU](#azureml-pytorch-11-gpu)
- [AzureML PyTorch 1,2 processzor](#azureml-pytorch-12-cpu)
- [AzureML PyTorch 1,2 GPU](#azureml-pytorch-12-gpu)
- [AzureML PyTorch 1,3 processzor](#azureml-pytorch-13-cpu)
- [AzureML PyTorch 1,3 GPU](#azureml-pytorch-13-gpu)
- [AzureML PyTorch 1,4 processzor](#azureml-pytorch-14-cpu)
- [AzureML PyTorch 1,4 GPU](#azureml-pytorch-14-gpu)
- [AzureML PyTorch 1,5 processzor](#azureml-pytorch-15-cpu)
- [AzureML PyTorch 1,5 GPU](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>AzureML PyTorch 1,0 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>AzureML PyTorch 1,0 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch 1,1 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>AzureML PyTorch 1,1 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>AzureML PyTorch 1,2 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>AzureML PyTorch 1,2 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>AzureML PyTorch 1,3 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>AzureML PyTorch 1,3 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - zseblámpa = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>AzureML PyTorch 1,4 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>AzureML PyTorch 1,4 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>AzureML PyTorch 1,5 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>AzureML PyTorch 1,5 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

## <a name="scikit-learn"></a>Scikit megismerése

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit – a 0.20.3 megismerése

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - scikit – Learn = = 0.20.3
  - SciPy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [Azure ML TensorFlow 1,10 processzor](#azureml-tensorflow-110-cpu)
- [Azure ML TensorFlow 1,10 GPU](#azureml-tensorflow-110-gpu)
- [Azure ML TensorFlow 1,12 processzor](#azureml-tensorflow-112-cpu)
- [Azure ML TensorFlow 1,12 GPU](#azureml-tensorflow-112-gpu)
- [Azure ML TensorFlow 1,13 processzor](#azureml-tensorflow-113-cpu)
- [Azure ML TensorFlow 1,13 GPU](#azureml-tensorflow-113-gpu)
- [Azure ML TensorFlow 2,0 processzor](#azureml-tensorflow-20-cpu)
- [Azure ML TensorFlow 2,0 GPU](#azureml-tensorflow-20-gpu)
- [Azure ML TensorFlow 2,1 processzor](#azureml-tensorflow-21-cpu)
- [Azure ML TensorFlow 2,1 GPU](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>AzureML TensorFlow 1,10 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>AzureML TensorFlow 1,10 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow – GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>AzureML TensorFlow 1,12 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1,12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>AzureML TensorFlow 1,12 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow – GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow 1,13 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>AzureML TensorFlow 1,13 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow – GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>AzureML TensorFlow 2,0 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>AzureML TensorFlow 2,0 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow – GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>AzureML TensorFlow 2,1 processzor

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>AzureML TensorFlow 2,1 GPU

csomagok csatornái:
- Conda – hamisítás

függőségek
- Python = 3.6.2
- pip
  - azureml-Core = = 1.8.0
  - azureml – alapértékek = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-HyperDrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow – GPU = = 2.1.0
  - horovod = = 0.19.1

