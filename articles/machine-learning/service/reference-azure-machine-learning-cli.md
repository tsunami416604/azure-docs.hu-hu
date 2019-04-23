---
title: A Machine learning CLI-bővítmény
titleSuffix: Azure Machine Learning service
description: További információ az Azure Machine Learning CLI-bővítmény az Azure CLI-hez. Az Azure CLI-vel egy platformfüggetlen parancssori segédprogram, amely lehetővé teszi, hogy az erőforrások az Azure-felhőben. A Machine Learning-bővítmény lehetővé teszi, hogy az Azure Machine Learning szolgáltatás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2504ca9cb785529a9eab321c2521db46390632b7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789173"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>A CLI-bővítmény használata az Azure Machine Learning szolgáltatáshoz

Az Azure Machine Learning parancssori az való kiterjesztése a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), többplatformos parancssori felület az Azure platformon. Ez a bővítmény használata az Azure Machine Learning szolgáltatással, a parancssorból parancsokat tartalmaz. Lehetővé teszi a teljes gépi tanulási munkafolyamatokat automatizáló szkriptek létrehozására. Ha például hozhat létre parancsprogramokat, amelyek a következő műveletek végrehajtásához:

+ Futtasson kísérleteket a machine learning-modellek létrehozása

+ Regisztráljon machine learning-modellek a vásárlók általi használatra

+ Csomagolását, üzembe helyezése és a gépi tanulási modellek életciklusának nyomon követése

A CLI nem helyettesíti a az Azure Machine Learning SDK-t. Egy kiegészítő eszköz, például a magas paraméteres feladatok kezelésére van optimalizálva:

* A számítási erőforrások létrehozása

* a paraméteres kísérlet beküldése

* Modell-regisztráció

* Lemezkép létrehozása

* Szolgáltatástelepítés

## <a name="prerequisites"></a>Előfeltételek


* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* A [az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Amikor a rendszer kéri, válassza ki a `y` a bővítmény telepítésére.

Győződjön meg arról, hogy a bővítmény telepítve van-e, használja a következő parancsot egy Machine Learning-specifikus almenüpontok listájának megjelenítéséhez:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Frissíteni kell a bővítményt __eltávolítása__ , majd __telepítése__ azt. Ez telepíti a legújabb verzióra.

## <a name="remove-the-extension"></a>Távolítsa el a bővítményt

A CLI-bővítmény eltávolításához használja a következő parancsot:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Erőforrás-kezelés

A következő parancsok bemutatják, hogyan használják az Azure Machine Learning-erőforrások kezelése a parancssori felület használatával.


+ Hozzon létre egy Azure Machine Learning szolgáltatás munkaterület:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Állítsa be egy alapértelmezett munkaterületet:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
+ AKS-fürt csatolása

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Kísérletek

A következő parancsok bemutatják, hogyan használhatja a parancssori felület kísérletek:

* A projekt (konfiguráció futtatása) csatolása a kísérlet elküldése előtt:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* A kísérlet egy Futtatás elindításához. Ez a parancs használatakor adja meg, amely tartalmazza a futtatási konfigurációtól runconfig fájl nevére. A számítási célnak a modell a betanítási környezetet hozhat létre, a futtatási konfigurációt használja. Ebben a példában a futtatási konfigurációtól betöltődnek az `./aml_config/myrunconfig.runconfig` fájlt.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    A runconfig fájl további információkért lásd: a [RunConfig](#runconfig) szakaszban.

* Az elküldött kísérletek listájának megtekintéséhez:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Modell regisztrációs, rendszerképek létrehozását és üzembe helyezés

A következő parancsok bemutatják, hogyan lehet regisztrálni egy betanított modellt, és majd éles szolgáltatásként üzembe:

+ Regisztrálja a modellt az Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ A gépi tanulási modell és a függőségeit tartalmazó lemezképek létrehozásához: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Egy rendszerkép üzembe helyezése az számítási célt:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Runconfig fájl

Egy futtatási konfigurációt a modell betanításához használja a oktatókörnyezet konfigurálására szolgál. Ez a konfiguráció memóriabeli az SDK-val hozhatók létre, és betölthetők runconfig fájlból.

Runconfig fájl szöveges dokumentum, amely leírja a konfiguráció a képzési környezet. Ha például a nevét a tanítási szkriptet és a modell betanításához szükséges conda-függőségeket tartalmazó fájl listáját.

Az Azure Machine Learning CLI létrehoz két alapértelmezett `.runconfig` nevű fájlt a `docker.runconfig` és `local.runconfig` mikor csatolása a projekthez használt a `az ml project attach` parancsot. 

Ha telepítette a kódot, amely létrehozza egy futtatási konfigurációt az a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) osztály, használhatja a `save()` metódus megőrizni, hogy egy `.runconfig` fájlt.

Az alábbiakban a tartalmát egy példát egy `.runconfig` fájlt:

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.4
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```
