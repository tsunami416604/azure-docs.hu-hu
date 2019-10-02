---
title: A DSVM található eszközök
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
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803942"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Milyen eszközöket tartalmaz az Azure Data Science Virtual Machine?

Alább található a Data Science Virtual Machine található eszközök naprakész listája, valamint a hivatkozásokat, amelyekkel megismerheti az egyes eszközök konfigurálásának módját.


| **Eszköz**                                                           | **A DSVM Windows** | **Linuxos DSVM** | **Használati megjegyzések** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) előre telepített népszerű csomagokkal   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [Az R használata a DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A fejlesztői kiadás tartalma: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy csomagjai](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagy teljesítményű keretrendszer (R és Python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), új legkorszerűbb gépi tanulási algoritmusok a Microsofttól <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R és Python operacionalizálási](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus megosztott aktiválással: Excel, Word és PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 és 3,5 az előre telepített népszerű csomagokkal    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előre telepített népszerű csomagokkal a Julia nyelvhez                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [A Julia használata a DSVM](./dsvm-languages.md#julia) |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-Data-Platforms # SQL-Server-2016-Developer-edition.md) |
| Adatbáziseszközök                                                       |  SQL Server Management Studio <br/> SQL Server integrációs szolgáltatás<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-illesztőprogramok|  [Mókus SQL](http://squirrel-sql.sourceforge.net/) (lekérdezési eszköz), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-illesztőprogramok| |
| Méretezhető adatbázis-elemzés SQL Server Machine learning-szolgáltatásokkal (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter notebook-kiszolgáló](https://jupyter.org/) a következő kernelekkel:                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Jupyter Notebook minták](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [R Jupyter minták](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Python Jupyter-minták](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Julia Jupyter-minták](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-minták](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Csak Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Sparker     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (többfelhasználós notebook-kiszolgáló)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (többfelhasználós notebook-kiszolgáló) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Csak Ubuntu) | |
| Fejlesztői eszközök, ide-kódok és kód-szerkesztők:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) git beépülő modullal, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)és [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio 2019 a DSVM](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [A Visual Studio Code a DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [RStudio Desktop a DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-kiszolgáló](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-kiszolgáló a DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Notebookshoz Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Notebookshoz a DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia ide)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Juno a DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Vim és Emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 git és git bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET-keretrendszer |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| SDK-k az Azure és a Cortana Intelligence szolgáltatáscsomag eléréséhez |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Asztali Power BI a DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Adatáthelyezési és-felügyeleti eszközök: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure Storage Explorer |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Azcopy a DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [blob Fuse illesztőprogram](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [blobfuse a DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Adlcopy a DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos db áttelepítési eszköz](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB a DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft adatkezelés Gateway](https://msdn.microsoft.com/library/dn879362.aspx): az adatáthelyezés a helyszíni és a felhő között |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM-használat](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 UNIX/Linux parancssori eszköz |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| Az [Apache-részletezés](https://drill.apache.org) az adatfeltáráshoz |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Apache-részletezés a DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Gépi tanulási eszközök: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 integráció a következővel: [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [XGBoost a DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Vowpal Wabbit a DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [WEKA](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [csörgő](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [WEKA a DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Csak Ubuntu) | [LightGBM a DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Csak Ubuntu) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [szénsavas víz](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Csak Ubuntu) | [H20 a DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| A GPU vagy a CPU-t használó Deep learning-eszközök: |  |  | A használattal kapcsolatos további információkért kattintson az alábbi eszköz nevére. |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span>(Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>Ubuntu | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Cafe és Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [lánc](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [kerasz](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow kiszolgáló](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, NVIDIA illesztőprogram](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
