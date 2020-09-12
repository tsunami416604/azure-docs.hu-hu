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
ms.topic: reference
ms.date: 09/03/2020
ms.openlocfilehash: 4ae96976f81aab9a0949594551c82d3a3fec4f0a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662079"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning kurátori környezetek

Ez a cikk a Azure Machine Learning, valamint a bennük előre telepített csomagokat és csatornákat sorolja fel. A Azure Machine Learning által biztosított, és a munkaterületen alapértelmezés szerint elérhetővé tett környezetek. Ezek a gyorsítótárban lévő Docker-rendszerképekből származnak, ami csökkenti a Futtatás előkészítési költségeit, és lehetővé teszi a gyorsabb üzembe helyezési időt. Ezekkel a környezetekkel gyorsan megkezdheti a különböző gépi tanulási keretrendszerek használatát.

> [!NOTE]
> Ez a lista szeptember 2020-én frissül. Használja a Python SDK-t a legfrissebb lista beszerzéséhez. További információ a [környezetekről szóló cikkben](./how-to-use-environments.md#use-a-curated-environment)található.

## <a name="azureml-automl"></a>AzureML – AutoML

**Csomagok csatornái:**

* anaconda
* Conda – hamisítás
* pytorch

**Conda csomagok:**

* python
* numpy
* scikit-learn
* pandas
* xgboost
* fbprophet
* ünnepek
* setuptools – git
* psutil

**Pip-csomagok:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – alapértékek
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* következtetés – séma
* cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Csomagok csatornái:**

* anaconda
* Conda – hamisítás
* pytorch

**Conda csomagok:**

* python
* numpy
* scikit-learn
* pandas
* xgboost
* fbprophet
* ünnepek
* setuptools – git
* pytorch
* cudatoolkit
* psutil

**Pip-csomagok:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – alapértékek
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* következtetés – séma
* pytorch – transzformátorok
* tágas
* en_core_web_sm
* cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Csomagok csatornái:**

* anaconda
* Conda – hamisítás
* pytorch

**Conda csomagok:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* ünnepek
* setuptools – git
* pytorch
* cudatoolkit
* psutil

**Pip-csomagok:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – alapértékek
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* következtetés – séma
* horovod
* pytorch – transzformátorok
* tágas
* en_core_web_sm
* cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-vízió-GPU

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – adatkészlet – futtatókörnyezet
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml – alapértékek
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-automl-DNN-vízió

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Csomagok csatornái:**

* anaconda
* Conda – hamisítás
* pytorch

**Conda csomagok:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* ünnepek
* setuptools – git
* psutil

**Pip-csomagok:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – alapértékek
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* következtetés – séma
* cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* chainert
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* chainert
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**Csomagok csatornái:**

* Conda – hamisítás
* pytorch
* alapértelmezések

**Conda csomagok:**

* python

**Pip-csomagok:**

* adlfs
* azureml-core
* azureml – adatkészlet – futtatókörnyezet
* dask [teljes]
* dask – ml [kész]
* elosztott
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python
* matplotlib

**Pip-csomagok:**

* azureml – alapértékek
* adlfs
* azureml-core
* dask [teljes]
* dask – ml [kész]
* elosztott
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-HyperDrive-ForecastDNN

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – alapértékek
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-automl-DNN-előrejelzés

## <a name="azureml-minimal"></a>AzureML – minimális

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* fáklya
* torchvision
* MKL
* horovod
* tensorboard
* jövőben

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-Learn-0.20.3

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1,10-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1,10-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1,12-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1,12-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1,13-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1,13-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2,0-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2,0-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2,1-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2,1-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2,2-CPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2,2-GPU

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML – oktatóanyag

**Csomagok csatornái:**

* anaconda
* Conda – hamisítás

**Conda csomagok:**

* python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml-telemetry
* azureml-Train-restclients-HyperDrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml – képzés
* azureml – SDK
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn – pandák

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Csomagok csatornái:**

* Conda – hamisítás

**Conda csomagok:**

* python

**Pip-csomagok:**

* azureml-core
* azureml – alapértékek
* azureml – adatkészlet – futtatókörnyezet [Fuse, pandák]
