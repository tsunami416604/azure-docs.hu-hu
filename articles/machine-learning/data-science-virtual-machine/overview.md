---
title: A Linux és Windows rendszerű Azure Data Science Virtual Machine (adatelemző virtuális gép) bemutatása | Microsoft Docs
description: Alapvető elemzési helyzetek és összetevők Windows és Linux rendszerű adatelemző virtuális gépekhez.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 8b2180b09267a10479bf5b16be9b43d6cb6310a2
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428844"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>A Linux és Windows rendszerre készült Azure Data Science Virtual Machine (DSVM, adatelemző virtuális gép) bemutatása

Az Adatelemző virtuális gép (DSVM) egy személyre szabott virtuálisgép-lemezkép a Microsoft Azure-felhőben, amelyet kifejezetten adatelemzésre hoztak létre. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. Windows Server és Linux rendszeren érhető el. A DSVM Windows-kiadását Server 2016 és Server 2012 rendszeren tesszük elérhetővé. A DSVM linuxos kiadásait Ubuntu 16.04 LTS és CentOS 7.4 rendszerre kínáljuk.

Ez a témakör az adatelemző virtuális géppel végrehajtható feladatokat tárgyalja, körvonalazza a virtuális gép néhány alapvető alkalmazási helyzetét, elemenként ismerteti a windowsos és linuxos változatokon elérhető fő funkciókat, és útmutatást nyújt a használatba vételükhöz.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Mire használhatom az adatelemző virtuális gépet?
A Data Science Virtual Machine (DSVM) rendeltetése az, hogy könnyen használható, előre konfigurált és teljes körűen integrált adatelemzési környezetet biztosítson a legkülönfélébb képzettségű és a legkülönfélébb iparágakban dolgozó adatszakértőknek. Saját hasonló munkakörnyezet kialakítása helyett üzembe helyezhet egy adatelemző virtuális gépet, így napokat vagy akár _heteket_ is megtakaríthat a telepítés, konfigurálás és csomagkezelés folyamatában. A DSVM üzembe helyezése után azonnal munkához láthat adatelemzési projektjén.

Az adatelemző virtuális gépet úgy tervezték és konfigurálták, hogy sokféle alkalmazási helyzetben használható legyen. A környezetet a projekt követelményeinek megfelelően felfelé vagy lefelé méretezheti, az adatelemzési műveleteket a kívánt nyelven programozhatja, és további eszközök telepítésével az igényeihez szabhatja a rendszert.

## <a name="key-scenarios"></a>Főbb alkalmazási helyzetek
Ez a szakasz néhány alapvető alkalmazási helyzetet javasol, amelyben az adatelemző virtuális gép üzembe helyezhető.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Előre konfigurált felhőbeli elemzési kezelőfelület
Az adatelemző virtuális gép alapszintű konfigurációt kínál azoknak az adatszakértőknek, akik helyszíni számítógépeiket felügyelt felhőbeli kezelőfelületre szeretnék cserélni. Ez az alapszint biztosítja, hogy egy csapat valamennyi adatszakértője egységes beállításokkal ellenőrizhesse a kísérleti eredményeket, és elősegíti az együttműködést. A rendszergazdák terhelését csökkentve a költségeket is kíméli, és megtakarítja a fejlett elemzésekhez szükséges különböző szoftvercsomagok értékeléséhez, telepítéséhez és üzemeltetéséhez szükséges időt.  

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás
A vállalati oktatók és adatszakértőket tanító előadók általában egy virtuálisgép-lemezképpel érik el, hogy tanítványaik egységes beállításokkal dolgozzanak, és a minták kiszámíthatóan működjenek. Az adatelemző virtuális gép igény szerinti környezetet hoz létre egységes beállításokkal, amely megkönnyíti a támogatást és az inkompatibilitási nehézségek leküzdését. Olyan esetekben, amikor gyakran kell környezetet kiépíteni, különösen a rövidebb kurzusokhoz, ez jelentős előnnyel jár.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Igény szerinti rugalmas kapacitás nagyszabású projektekhez
Az adatelemzési ötletbörzékhez/versenyekhez és a nagytömegű adatok elemzéséhez és feltárásához bővebb hardverkapacitás szükséges, általában rövid időre. Az adatelemző virtuális gép igény szerint gyorsan replikálja az adatelemzési környezetet olyan horizontálisan bővített kiszolgálókon, amelyek lehetővé teszik a nagy számításierőforrás-igényű kísérleteket.

### <a name="short-term-experimentation-and-evaluation"></a>Rövidtávú kísérletezés és kiértékelés
Az adatelemző virtuális gép minimális előkészítéssel felhasználható olyan eszközök felmérésére és megismerésére, mint a Microsoft ML Server, az SQL Server, a Visual Studio eszközei, a Jupyter, a deep learning / ML eszközkészletek és a közösségben népszerű új eszközök. Mivel a Data Science VM gyorsan beállítható, olyan egyéb rövidtávú alkalmazási helyzetekben is felhasználható, mint a közzétett kísérletek replikálása, bemutatók futtatása, online foglalkozások útmutatásainak követése vagy konferenciaképzések megtartása.

### <a name="deep-learning"></a>Deep learning
A Data Science VM-en modelleket taníthat be a grafikus processzorokon (GPU) futtatott mélytanulási algoritmusok segítségével. Az Azure-felhőbeli virtuálisgép-méretezési lehetőségeket kihasználva a DSVM segít a felhőbeli GPU-alapú hardver igény szerinti használatában. Nagy modellek betanításakor vagy ha gyors számításokra van szükség, az operációs rendszerlemez megtartásával lehet áttérni GPU-alapó virtuális gépre.  A DSVM Windows Server 2016-os kiadása az előre telepített GPU-illesztőprogramokat, keretrendszereket és deep learning keretrendszerek GPU-verzióit is tartalmazza. A linuxos kiadáson a GPU-alapú mélytanulás a CentOS-es és ubuntus DSVM-eken is engedélyezve van. A Data Science VM ubuntus, CentOS-es vagy Windows 2016-os kiadása nem GPU-alapú Azure-beli virtuális gépekre is telepíthető. Ilyenkor a mélytanulási keretrendszer visszaáll CPU-üzemmódra. 

## <a name="whats-included-in-the-data-science-vm"></a>Mi tartozik az adatelemző virtuális géphez?
Az adatelemző virtuális gépen sok közkedvelt adatelemzési és deep learning eszköz már telepítve és konfigurálva van. Olyan eszközöket is tartalmaz, amelyek megkönnyítik a különböző Azure adat- és elemzési termékek használatát. Ilyen például a Microsoft ML Server (R, Python) a prediktív modellek összeállításához vagy az SQL Server 2017 a nagyméretű adatkészletek feltárásához. Tartalmaz még a nyílt forráskód közösségtől és a Microsofttól származó sokféle eszközt, mintakódokat és jegyzettömböket. Az alábbi táblázat elemenként mutatja be és hasonlítja össze az adatelemző virtuális gép windowsos és linuxos kiadásainak fő összetevőit.


| **Eszköz**                                                           | **Windowsos kiadás** | **Linuxos kiadás** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) előre telepített népszerű csomagokkal   |I                      | I             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition melynek része, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagyteljesítményű keretrendszer (R és Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - A legkorszerűbb új ML-algoritmusok a Microsofttól <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R és Python operacionalizálás](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |I                      | I |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus megosztott aktiválással - Excel, Word és PowerPoint   |I                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 előre telepített népszerű csomagokkal    |I                      |I              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előre telepített népszerű csomagokkal a Julia nyelvhez                         |I                      |I              |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Adatbáziseszközök                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC illesztőprogramok| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (lekérdező eszköz), <br /> * bcp, sqlcmd <br /> * ODBC/JDBC illesztőprogramok|
| Méretezhető adatbázison belüli elemzések SQL Server ML szolgáltatásokkal (R, Python) | I     |N              |
| **[Jupyter Notebook Server](http://jupyter.org/) a következő kernelekkel,**                                  | I     | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | I (csak Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | I |
| JupyterHub (többfelhasználós notebook server)| N | I |
| JupyterLab (többfelhasználós notebook server) | N | I (csak Ubuntu) |
| **Fejlesztőeszközök, IDE-k és kódszerkesztők**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) Git bővítménnyel, Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs), és [R Tools for Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim és Emacs | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git és GitBash | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net keretrendszer | I | N |
| PowerBI Desktop | I | N |
| SDK-k az Azure és a Cortana Intelligence szolgáltatáscsomag eléréséhez | I | I |
| **Adatáthelyezési és felügyeleti eszközök** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE illesztőprogram](https://github.com/Azure/azure-storage-fuse) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB adatáttelepítési eszköz](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): Adatok áthelyezése az OnPrem és a felhő között | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux parancssor eszközök | I | I |
| [Apache Drill](http://drill.apache.org) adatok feltárásához | I | I |
| **Machine Learning eszközök** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integráció az [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) szolgáltatással (R, Python) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/), [Deep Water](https://www.h2o.ai/deep-water/) | N | I (csak Ubuntu) |
| **Deep Learning eszközök** <br>Minden eszköz működik GPU-n és CPU-n is |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) (Windows 2016) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | I (Windows 2016) | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | I (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | I (Windows 2016) | I|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, NVIDIA illesztőprogram](https://developer.nvidia.com/cuda-toolkit) | I | I |
| **Big Data Platform (csak Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Helyszíni különálló [Spark](http://spark.apache.org/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Local [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | N | I |

## <a name="get-started"></a>Bevezetés

### <a name="windows-data-science-vm"></a>Windows rendszerű adatelemző virtuális gép
* Windowsos DSVM létrehozásáról és használatáról a [Windows rendszerű adatelemző virtuális gép üzembe helyezése](provision-vm.md) című cikk nyújt bővebb tájékoztatást. Az adatelemzési projektjéhez szükséges különböző tevékenységek windowsos adatelemző virtuális gépen való végrehajtásának módjáról a [Tíz dolog, amit megtehet az adatelemző virtuális géppel](vm-do-ten-things.md) című cikk ír bővebben.

### <a name="linux-data-science-vm"></a>Linux rendszerű adatelemző virtuális gép
* Ubuntus DSVM létrehozásáról és használatáról az [Adatelemző virtuális gép üzembe helyezése Linuxon (Ubuntu)](dsvm-ubuntu-intro.md) című cikk nyújt bővebb tájékoztatást. CentOS-es DSVM létrehozásáról és használatáról az [Linux CentOS-es adatelemző virtuális gép üzembe helyezése az Azure-on](linux-dsvm-intro.md) című cikk nyújt bővebb tájékoztatást.
* A gyakori adatelemzési feladatok végrehajtását CentOS és Ubuntu Linux rendszeren is bemutató útmutatást talál az [Adatelemzés a linuxos adatelemző virtuális gépen](linux-dsvm-walkthrough.md) című cikkben.

## <a name="next-steps"></a>További lépések
[Az Azure R fejlesztői útmutatója](../r-developers-guide.md)
