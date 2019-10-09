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
ms.date: 09/27/2019
ms.openlocfilehash: 68c48bd4288b80d6cab515aa981d0f72461c0849
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035265"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Milyen eszközöket tartalmaz az Azure Data Science Virtual Machine?

Alább található a Data Science Virtual Machine található eszközök naprakész listája, valamint a hivatkozásokat, amelyekkel megismerheti az egyes eszközök konfigurálásának módját.


| **Eszköz**                                                           | **A DSVM Windows** | **Linuxos DSVM** | **Használati megjegyzések** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) előre telepített népszerű csomagokkal   |<span class='green-check'>&#9989;</span></br> 3\.4.3                    |<span class='green-check'>&#9989;</span></br> 3\.4.3  | [R a DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A fejlesztői kiadás tartalma: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy csomagjai](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagy teljesítményű keretrendszer (R és Python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), új legkorszerűbb gépi tanulási algoritmusok a Microsofttól <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R és Python operacionalizálási](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> 9\.3.0                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus megosztott aktiválással: Excel, Word és PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 és 3,5 az előre telepített népszerű csomagokkal    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előre telepített népszerű csomagokkal a Julia nyelvhez                         |<span class='green-check'>&#9989;</span> </br> 0\.6.4                   |<span class='green-check'>&#9989;</span></br> 0\.6.2              | [Julia a DSVM](./dsvm-languages.md#julia) |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server a DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Adatbáziseszközök                                                       |  SQL Server Management Studio <br/> SQL Server integrációs szolgáltatás<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-illesztőprogramok|  [Mókus SQL](http://squirrel-sql.sourceforge.net/) (lekérdezési eszköz), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-illesztőprogramok| |
| Méretezhető adatbázis-elemzés SQL Server Machine learning-szolgáltatásokkal (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter notebook-kiszolgáló](https://jupyter.org/) a következő kernelekkel:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Jupyter Notebook minták](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R |<span class='green-check'>&#9989;</span></br> 3\.4.3 |<span class='green-check'>&#9989;</span></br> 3\.4.3 | [R Jupyter minták](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Python |<span class='green-check'>&#9989;</span></br> (3) |<span class='green-check'>&#9989;</span> | [Python Jupyter-minták](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span></br> 0\.6.4 |<span class='green-check'>&#9989;</span></br> 0\.6.2 | [Julia Jupyter-minták](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-minták](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Csak Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Sparker     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (többfelhasználós notebook-kiszolgáló)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (többfelhasználós notebook-kiszolgáló) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Fejlesztői eszközök, ide-kódok és kód-szerkesztők:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) git beépülő modullal, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)és [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [A Visual Studio a DSVM](./dsvm-tools-development.md#visual-studio-2017) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [A Visual Studio Code a DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.1.463) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio Desktop a DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-kiszolgáló](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-kiszolgáló a DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Notebookshoz Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (2018.1.4)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [Notebookshoz a DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia ide)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno a DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> 8\.1.5 |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [git](https://git-scm.com/) és git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> 0\.6.2 | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0 _201) |<span class='green-check'>&#9989;</span></br> (1.8.0 _222) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET-keretrendszer |<span class='green-check'>&#9989;</span></br> 4\.7.2 |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.65.5313.1381) |<span class='red-x'>&#10060;</span>   |
| SDK-k az Azure és a Cortana Intelligence szolgáltatáscsomag eléréséhez |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Asztali Power BI a DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Adatáthelyezési és-felügyeleti eszközök: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> 1\.6.2 |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> 8\.1.0 |<span class='red-x'>&#10060;</span>   | [Azcopy a DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [blob Fuse illesztőprogram](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse a DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy a DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos db áttelepítési eszköz](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB a DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 UNIX/Linux parancssori eszköz |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| Az [Apache-részletezés](https://drill.apache.org) az adatfeltáráshoz |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache-részletezés a DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Gépi tanulási eszközök: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 integráció a következővel: [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> 0\.2.7 |<span class='green-check'>&#9989;</span></br> 1\.0.45 | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost a DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit a DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [WEKA](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> 3\.8.0 |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [csörgő](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> 5\.1.0 |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [WEKA a DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.1.2 | [LightGBM a DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 0\.10.3 | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [szénsavas víz](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 a DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| A GPU vagy a CPU-t használó Deep learning-eszközök: |  |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Cafe](https://github.com/BVLC/caffe) és [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | A DSVM [Cafe](./dsvm-deep-learning-ai-frameworks.md#caffe) és [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [lánc](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 5\.2.0 | [A DSVM láncolása](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, NVIDIA illesztőprogram](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, NVIDIA-illesztőprogram a DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod a DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [kerasz](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.2.4 | [Kerasz a DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK a DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.3.0 | [MXNet a DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [MXNet-modell kiszolgálója a DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA Rendszerfelügyeleti felület (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [NVIDIA-SMI a DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch a DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [TensorFlow a DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow kiszolgáló](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [A DSVM szolgáló TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> 1\.0.3 | [Theano a DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

