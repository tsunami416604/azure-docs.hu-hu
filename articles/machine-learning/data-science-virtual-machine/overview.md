---
title: A Linux és a Windows rendszerhez készült Azure Data Science Virtual Machine bemutatása | Microsoft Docs
description: Alapvető elemzési helyzetek és összetevők Windows és Linux rendszerű adatelemző virtuális gépekhez.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 22192d67a946f9f466848b65b9cd763b16e6c5f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099368"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Mi a Linux és a Windows rendszerhez készült Azure Data Science Virtual Machine?

A Data Science Virtual Machine (DSVM) egy testreszabott virtuálisgép-rendszerkép az Azure Cloud platformon, amely kifejezetten az adatelemzéshez készült. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. 

Az eszközök konfigurációit a Microsoft és a szélesebb körű adatelemzési Közösség által készített adatszakértők és fejlesztők szigorúan tesztelték. Ez a teszt segít a stabilitás és az általános életképesség biztosításában.

A DSVM a következő címen érhető el:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS és CentOS 7,4

> [!NOTE]
> A Deep learninghez készült összes virtuálisgép-eszköz be lett hajtva a Data Science Virtual Machineba. 


## <a name="what-can-i-do-with-the-dsvm"></a>Mire használhatom a DSVM?
A Data Science Virtual Machine célja, hogy az szakembereket az összes képzettségi szint és az egész iparág számára biztosítson, amely súrlódás nélküli, előre konfigurált és teljes mértékben integrált adatelemzési környezettel rendelkezik. Ahelyett, hogy saját maga is kiépít egy hasonló munkaterületet, létrehozhat egy DSVM. Ez a választás a telepítés, a konfiguráció és a csomag felügyeleti folyamatainál akár napokat, akár heteket is megtakaríthat. A DSVM üzembe helyezése után azonnal munkához láthat adatelemzési projektjén.

A DSVM úgy van kialakítva és konfigurálva, hogy a használati forgatókönyvek széles körének megfelelően működjön. A környezet követelményeinek megváltozása mellett akár le is méretezheti a környezetet. Az adatelemzési feladatok elvégzéséhez használhatja az előnyben részesített nyelvet is, és más eszközöket is telepíthet a rendszer igényei szerinti testreszabásához.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Előre konfigurált felhőbeli elemzési kezelőfelület
A DSVM olyan adatelemző csapatok számára biztosít alapkonfigurációt, amelyek a helyi asztalait egy felügyelt felhőalapú asztallal szeretnék helyettesíteni. Ez az alapszint biztosítja, hogy egy csapat valamennyi adatszakértője egységes beállításokkal ellenőrizhesse a kísérleti eredményeket, és elősegíti az együttműködést. A rendszergazdai terhek csökkentésével csökkenti a költségeket is. Ez a terhek csökkentése a speciális analitikai szoftvercsomagok kiértékeléséhez, telepítéséhez és karbantartásához szükséges időt takarít meg.

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás
Az adatelemzési osztályokat tanító vállalati oktatók és oktatók általában virtuálisgép-rendszerképet biztosítanak. A rendszerkép biztosítja, hogy a tanulók konzisztens beállításokkal rendelkezzenek, és hogy a minták kiszámíthatóan működjenek. 

A DSVM egy igény szerinti környezetet hoz létre egy konzisztens beállítással, amely megkönnyíti a támogatási és kompatibilitási kihívásokat. Olyan esetekben, amikor gyakran kell környezetet kiépíteni, különösen a rövidebb kurzusokhoz, ez jelentős előnnyel jár.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Igény szerinti rugalmas kapacitás nagyszabású projektekhez
Az adatelemzési ötletbörzékhez/versenyeken, illetve a nagyméretű adatmodellezésen és-kutatáson túl nagy kapacitást kell kibővíteni, jellemzően rövid időtartamra. A DSVM segítségével gyorsan, igény szerint replikálhatja az adatelemzési környezetet, a nagy teljesítményű számítástechnikai erőforrások által futtatható kísérleteket lehetővé tevő kibővített kiszolgálókon.

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks egyéni számítási teljesítmény
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) egy ingyenes üzemeltetett szolgáltatás, amely a felhőben Jupyter-jegyzetfüzeteket fejleszt, futtat és oszt meg a telepítés nélkül. Az ingyenes szolgáltatási csomag legfeljebb 4 GB memóriával és 1 GB adattal rendelkezik. 

Az összes korlát felszabadításához csatolhat jegyzetfüzet-projektet egy DSVM vagy bármely más, a Jupyter-kiszolgálón futó virtuális géphez. Ha Azure Active Directory (például vállalati) fiókkal jelentkezik be a Azure Notebooksba, a jegyzetfüzetek automatikusan megjeleníti a fiókhoz társított előfizetések Dsvm. A rendelkezésre álló számítási teljesítmény kibontásához [DSVM csatolhat Azure Notebookshoz](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) .

### <a name="short-term-experimentation-and-evaluation"></a>Rövidtávú kísérletezés és kiértékelés
A DSVM segítségével kiértékelheti vagy megszerezheti az eszközökhöz hasonló eszközöket, minimális beállítási erőfeszítéssel:

- Microsoft Machine Learning-kiszolgáló
- SQL Server
- Visual Studio-eszközök
- Jupyter
- Mély tanulási és gépi tanulási segédanyagok
- A Közösségben népszerű új eszközök 

Mivel a DSVM gyorsan beállíthatja, azt más rövid távú használati helyzetekben is alkalmazhatja. Ezek közé tartozik például a közzétett kísérletek replikálása, a bemutatók végrehajtása, valamint az online munkamenetek és a konferencia-oktatóanyagok.

### <a name="deep-learning"></a>Deep learning
A DSVM a képzési modellek mély tanulási algoritmusokat használhatnak a grafikus feldolgozási egységeken (GPU-k) alapuló hardveren. Az Azure platform virtuálisgép-méretezési képességeinek kihasználásával a DSVM a Felhőbeli GPU-alapú hardverek igény szerinti használatát teszi lehetővé. Ha nagyméretű modelleket szeretne betanítani, váltson GPU-alapú virtuális gépre, vagy ha nagy sebességű számításokra van szüksége, miközben az operációs rendszer lemezét is megtartja.  

A DSVM Windows Server 2016-es kiadása előre telepítve van a GPU-illesztőprogramokkal,-keretrendszerekkel és a Deep learning-keretrendszerek GPU-verzióival. A Linux-kiadásban a GPU-k mélyreható megismerése engedélyezve van a CentOS és az Ubuntu Dsvm is. 

A DSVM Ubuntu, CentOS vagy Windows 2016 kiadását egy olyan Azure-beli virtuális gépre is üzembe helyezheti, amely nem a GPU-k alapján van. Ebben az esetben az összes mély tanulási keretrendszer vissza fog térni a CPU-módra.
 
[További információ az elérhető Deep learning-és AI-keretrendszerekről](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Mit tartalmaz a DSVM?
Az adatelemző virtuális gépen sok közkedvelt adatelemzési és deep learning eszköz már telepítve és konfigurálva van. Olyan eszközöket is tartalmaz, amelyek megkönnyítik a különböző Azure-beli adatkezelő és adatelemző eszközökkel végzett munkát. Ezen termékek közé tartoznak a Microsoft Machine Learning Server (R, Python) a prediktív modellek létrehozásához, valamint a 2017-es SQL Server az adathalmazok nagy léptékű feltárásához. A DSVM a nyílt forráskódú Közösségtől és a Microsofttól származó egyéb eszközöket, valamint a [mintakód és](dsvm-samples-and-walkthroughs.md)a jegyzetfüzetek szolgáltatásait is tartalmazza. 

Az alábbi lista az eszközöket és platformokat tartalmazza:
+ [Támogatott programozási nyelvek](dsvm-languages.md)

+ [Támogatott adatplatformok](dsvm-data-platforms.md)

+ [Fejlesztői eszközök és ide-](dsvm-tools-development.md)

+ [Mély tanulási és AI-keretrendszerek](dsvm-deep-learning-ai-frameworks.md)

+ [Gépi tanulási és adatelemzési eszközök](dsvm-ml-data-science-tools.md)

+ [Adatfeldolgozási eszközök](dsvm-tools-ingestion.md)

+ [Adatfelderítési és vizualizációs eszközök](dsvm-tools-explore-and-visualize.md)

Az alábbi táblázat elemenként mutatja be és hasonlítja össze az adatelemző virtuális gép windowsos és linuxos kiadásainak fő összetevőit.

| **Eszköz**                                                           | **Windows-kiadás** | **Linux-kiadás** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) előre telepített népszerű csomagokkal   |I                      | I             |
| [Microsoft Machine learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A fejlesztői kiadás tartalma: <br />  &nbsp;&nbsp;&nbsp;&nbsp;[RevoScaleR/revoscalepy csomagjai](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagy teljesítményű keretrendszer (R és Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;[MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), új legkorszerűbb gépi tanulási algoritmusok a Microsofttól <br />  &nbsp;&nbsp;&nbsp;&nbsp;[R-és Python-operacionalizálási](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |I                      | I |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus megosztott aktiválással: Excel, Word és PowerPoint   |I                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2,7 és 3,5 az előre telepített népszerű csomagokkal    |I                      |I              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előre telepített népszerű csomagokkal a Julia nyelvhez                         |I                      |I              |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Adatbáziseszközök                                                       |  SQL Server Management Studio <br/> SQL Server integrációs szolgáltatás<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-illesztőprogramok|  [Mókus SQL](http://squirrel-sql.sourceforge.net/) (lekérdezési eszköz), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-illesztőprogramok|
| Méretezhető adatbázis-elemzés SQL Server Machine learning-szolgáltatásokkal (R, Python) | I     |N              |
| [Jupyter notebook-kiszolgáló](https://jupyter.org/) a következő kernelekkel:                                  | I     | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;R | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;Python | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;Julia | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;PySpark | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;[Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | I (csak Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;SparkR     | N | I |
| JupyterHub (többfelhasználós notebook-kiszolgáló)| N | I |
| JupyterLab (többfelhasználós notebook-kiszolgáló) | N | I (csak Ubuntu) |
| Fejlesztői eszközök, ide-kódok és kód-szerkesztők:| | |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual studio 2019 (Community Edition)](https://www.visualstudio.com/community/) git beépülő modullal, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)és [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio Code](https://code.visualstudio.com/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[RStudio asztal](https://www.rstudio.com/products/rstudio/#Desktop) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[RStudio-kiszolgáló](https://www.rstudio.com/products/rstudio/#Server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Notebookshoz Community Edition](https://www.jetbrains.com/pycharm/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Atom](https://atom.io/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Juno (Julia ide)](https://junolab.org/)| I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;Vim és Emacs | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;Git és git bash | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;OpenJDK | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;.NET-keretrendszer | I | N |
| Power BI Desktop | I | N |
| SDK-k az Azure és a Cortana Intelligence szolgáltatáscsomag eléréséhez | I | I |
| Adatáthelyezési és-felügyeleti eszközök: | | |
| &nbsp;&nbsp;&nbsp;&nbsp;Azure Storage Explorer | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Azure CLI](https://docs.microsoft.com/cli/azure) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;Azure PowerShell | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Blob Fuse-illesztőprogram](https://github.com/Azure/azure-storage-fuse) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Adatáttelepítési eszköz Azure Cosmos db](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Microsoft adatkezelés átjáró](https://msdn.microsoft.com/library/dn879362.aspx): az adatáthelyezés a helyszíni és a felhő között | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;UNIX/Linux parancssori eszközök | I | I |
| Az [Apache-részletezés](https://drill.apache.org) az adatfeltáráshoz | I | I |
| Gépi tanulási eszközök: |||
| &nbsp;&nbsp;&nbsp;&nbsp;Integráció a [Azure Machine Learningsal](https://azure.microsoft.com/services/machine-learning/) (R, Python) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[XGBoost](https://github.com/dmlc/xgboost) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[WEKA](https://www.cs.waikato.ac.nz/ml/weka/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Csörgő](https://togaware.com/rattle/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[LightGBM](https://github.com/Microsoft/LightGBM) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[CatBoost](https://tech.yandex.com/catboost/) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[H2O](https://www.h2o.ai/h2o/), [szénsavas víz](https://www.h2o.ai/sparkling-water/) | N | I (csak Ubuntu) |
| A GPU vagy a CPU-t használó Deep learning-eszközök: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow](https://www.tensorflow.org/) | I (Windows 2016) | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Horovod](https://github.com/uber/horovod) | N | I (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[MXNet](https://mxnet.io/) | I (Windows 2016) | I|
| &nbsp;&nbsp;&nbsp;&nbsp;[Cafe és Caffe2](https://github.com/caffe2/caffe2) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Láncolási lánc](https://chainer.org/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Zseblámpa](http://torch.ch/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Theano](https://github.com/Theano/Theano) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[Kerasz](https://keras.io/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[PyTorch](https://pytorch.org/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[NVIDIA](https://github.com/NVIDIA/DIGITS) -számjegyek | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[MXNet-modell kiszolgálója](https://github.com/awslabs/mxnet-model-server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow-kiszolgáló](https://www.tensorflow.org/serving/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorRT](https://developer.nvidia.com/tensorrt) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;[CUDA, cuDNN, NVIDIA-illesztőprogram](https://developer.nvidia.com/cuda-toolkit) | I | I |

## <a name="next-steps"></a>További lépések

További információ ezekről a cikkekről:

+ Windows:
  + [Windowsos DSVM beállítása](provision-vm.md)
  + [Tíz dolog, amit elvégezhet a Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM (Ubuntu) beállítása](dsvm-ubuntu-intro.md)
  + [Linux DSVM (CentOS) beállítása](linux-dsvm-intro.md)
  + [Adatelemzés Linux-DSVM](linux-dsvm-walkthrough.md)
