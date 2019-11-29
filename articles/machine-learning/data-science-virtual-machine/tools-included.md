---
title: A DSVM-en elérhető eszközök
titleSuffix: Azure Data Science Virtual Machine tools
description: A Windows-és Ubuntu DSVM-lemezképekben található eszközök listája
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 2d5dc2d33252849dc0a3e11a65fc54e878dbb8a3
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555315"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Milyen eszközöket tartalmaz az Azure Data Science Virtual Machine?

Alább található a Data Science Virtual Machine található eszközök naprakész listája, valamint a hivatkozásokat, amelyekkel megismerheti az egyes eszközök konfigurálásának módját.


| **Eszköz**                                                           | **Windowsos DSVM** | **Linuxos DSVM** | **Használati megjegyzések** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) előre telepített népszerű csomagokkal   |<span class='green-check'>&#9989;</span></br> 3\.4.3                    |<span class='green-check'>&#9989;</span></br> 3\.4.3  | [R a DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A fejlesztői kiadás tartalma: <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy csomagjai](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagy teljesítményű keretrendszer (R és Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), új legkorszerűbb gépi tanulási algoritmusok a Microsofttól <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R és Python operacionalizálási](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> 9\.3.0                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://www.microsoft.com/p/office-365-proplus/CFQ7TTC0K8R0) ProPlus megosztott aktiválással: Excel, Word és PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 és 3,5 az előre telepített népszerű csomagokkal    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előre telepített népszerű csomagokkal a Julia nyelvhez                         |<span class='green-check'>&#9989;</span> </br> 0\.6.4                   |<span class='green-check'>&#9989;</span></br> 0\.6.2              | [Julia a DSVM](./dsvm-languages.md#julia) |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server a DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Adatbáziseszközök                                                       |  SQL Server Management Studio <br/> SQL Server integrációs szolgáltatás<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-illesztőprogramok|  [Mókus SQL](http://squirrel-sql.sourceforge.net/) (lekérdezési eszköz), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-illesztőprogramok| |
| Méretezhető adatbázis-elemzés SQL Server Machine learning-szolgáltatásokkal (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter notebook-kiszolgáló](https://jupyter.org/) a következő kernelekkel:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Jupyter Notebook minták](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> 3\.4.3 |<span class='green-check'>&#9989;</span></br> 3\.4.3 | [R Jupyter minták](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> 3 |<span class='green-check'>&#9989;</span> | [Python Jupyter-minták](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> 0\.6.4 |<span class='green-check'>&#9989;</span></br> 0\.6.2 | [Julia Jupyter-minták](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-minták](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Csak Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; Sparker     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (többfelhasználós notebook-kiszolgáló)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (többfelhasználós notebook-kiszolgáló) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Fejlesztői eszközök, ide-kódok és kód-szerkesztők:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual studio 2017 (Community Edition)](https://www.visualstudio.com/community/) git beépülő modullal, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)és [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [A Visual Studio a DSVM](./dsvm-tools-development.md#visual-studio-community-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [A Visual Studio Code a DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50 XX) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio Desktop a DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio-kiszolgáló](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-kiszolgáló a DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [Notebookshoz Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [Notebookshoz a DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia ide)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno a DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> 8\.1.5 |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [git](https://git-scm.com/) és git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> 0\.6.2 | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0 _201) |<span class='green-check'>&#9989;</span></br> (1.8.0 _222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET-keretrendszer |<span class='green-check'>&#9989;</span></br> 4\.7.2 |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/) |<span class='green-check'>&#9989;</span></br> (2.73.55 XX) |<span class='red-x'>&#10060;</span>   |
| SDK-k az Azure és a Cortana Intelligence szolgáltatáscsomag eléréséhez |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Asztali Power BI a DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Adatáthelyezési és-felügyeleti eszközök: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> 1\.10.1 |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> 8\.1.0 |<span class='red-x'>&#10060;</span>   | [Azcopy a DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [blob Fuse-illesztőprogram](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse a DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy a DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos db adatáttelepítési eszköz](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB a DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; UNIX/Linux parancssori eszközök |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| Az [Apache-részletezés](https://drill.apache.org) az adatfeltáráshoz |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache-részletezés a DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Gépi tanulási eszközök: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; integráció [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> 0\.2.7 |<span class='green-check'>&#9989;</span></br> 1\.0.45 | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost a DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit a DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [WEKA](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> 3\.8.0 |
| &nbsp;&nbsp;&nbsp;&nbsp; [csörgő](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> 5\.1.0 |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [WEKA a DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.1.2 | [LightGBM a DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 0\.10.3 | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [szénsavas víz](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 a DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| A GPU vagy a CPU-t használó Deep learning-eszközök: |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Cafe](https://github.com/BVLC/caffe) és [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | A DSVM [Cafe](./dsvm-deep-learning-ai-frameworks.md#caffe) és [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |
| &nbsp;&nbsp;&nbsp;&nbsp; [láncolt](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 5\.2.0 | [A DSVM láncolása](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA, cuDNN, NVIDIA illesztőprogram](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, NVIDIA-illesztőprogram a DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod a DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [kerasz](https://keras.io) |<span class='green-check'>&#9989;</span></br> 2\.2.4   |<span class='green-check'>&#9989;</span></br> 2\.2.4 | [Kerasz a DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK a DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.3.0 | [MXNet a DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [MXNet-modell kiszolgálója a DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVIDIA rendszerkezelési felület (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [NVIDIA-SMI a DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch a DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [TensorFlow a DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow-kiszolgáló](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [A DSVM szolgáló TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> 1\.0.3 | [Theano a DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

