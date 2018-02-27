---
title: "A Linux és a Windows Azure adatok tudományos virtuális gép bemutatása |} Microsoft Docs"
description: "Kulcs analytics forgatókönyvek és a Windows és Linux adatok tudományos virtuális gépek összetevői."
keywords: "adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 3cedc16e3a420fd846b7860b69f4b19591ad1777
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>A Linux és a Windows Azure adatok tudományos virtuális gép bemutatása

Az adatok tudományos virtuális gép (DSVM) a Microsoft Azure felhőbe kifejezetten a adattudomány beépített testreszabott Virtuálisgép-lemezképet. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. Elérhető Windows Server és Linux rendszeren. A DSVM Windows-kiadását Server 2016 és Server 2012 rendszeren tesszük elérhetővé. Ubuntu 16.04 LTS és CentOS 7.4 DSVM Linux kiadása fel.

Ez a témakör ismerteti, mire képes az adatok tudományos VM a, néhány fontos forgatókönyveinek használatával a virtuális gép, a Windows és Linux-verziókon érhető el a legfontosabb jellemzők részletezi és útmutatás őket az első lépéseiben.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Milyen feladatokat lehet elvégezni az adatok tudományos virtuálisgép?
Az adatok tudományos virtuális gép célja szakértelem szintek és a szerepkörök adatok szakemberei adja meg a tudományos adatok súrlódás szabad környezetben. Ez a virtuális gép, ha Ön volt összehasonlítható környezet saját töltött idő jelentős menti. Ehelyett a tudományos adatprojekthez azonnal elindítani az újonnan létrehozott Virtuálisgép-példány. 

Az adatok tudományos virtuális gép tervezett és használati forgatókönyvek széles körének való munkához konfigurálva van. Méretezheti a környezet felfelé vagy lefelé, a projekt igényeinek változását. Biztosan tudni használni a választott nyelv a program tudományos feladatok. Más eszközök telepítése, és testre szabhatja a rendszer pontos igényeinek.

## <a name="key-scenarios"></a>Főbb forgatókönyvek
Ez a szakasz javasol néhány főbb forgatókönyvek, amelynek a adatok tudományos virtuális gép is telepíthető.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>A felhő előre konfigurált analytics asztalához
Az adatok tudományos virtuális gép olyan alapvető konfigurációt kívánja cserélje le a helyi asztalok felügyelt felhőalapú asztali adatok tudományos csoportjai. Ennél az alaptervnél biztosítja, hogy a csoport összes adatszakértőkön egy egységes telepítő, amellyel a kísérletet, és együttműködés elősegítése. Csökkenti a sysadmin (rendszergazda) terheket, és mentse a kiértékeléséhez, telepítéséhez és a speciális elemzés szükséges különböző szoftvercsomagok karbantartása szükséges idő a költségek is csökkenti.  

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás
Vállalati oktatók és nevelők, amely mutatja meg a tudományos adatok osztályok általában adjon meg egy virtuálisgép-lemezkép biztosítja, hogy a diákok egységes telepítő és a minták kiszámítható működik. Az adatok tudományos VM egy igény szerinti környezetet egy egységes telepítő, amely megkönnyíti a támogatási és kompatibilitási kihívást hoz létre. Esetben, amikor ezekhez a környezetekhez kell kialakítani, gyakran, különösen a rövidebb képzési osztályok, jelentősen előnyeit.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Igény szerinti rugalmas kapacitás nagyméretű projektek
Adatok tudományos hackathons/versenyek vagy nagy méretű adatok modellezési és kutatási funkciójával szükséges horizontálisan felskálázott hardverének kapacitása, általában rövid időre. Az adatok tudományos VM segítségével replikálják az adatokat tudományos környezetet gyorsan az igény szerinti méretezett kimenő kiszolgálókon, amelyek lehetővé teszik a kísérletek igénylő futtatható nagy teljesítményű számítástechnikai erőforrások.

### <a name="short-term-experimentation-and-evaluation"></a>Rövid távú kísérletezhet és kiértékelése
Az adatok tudományos VM lehet segítségével értékelje ki, és ismerje meg, például a Microsoft ML Server, SQL Server-eszközök Visual Studio eszközök, Jupyter, mély tanulási / ML eszközök gazdag, és új eszközök népszerű közösségi a minimális erőfeszítéssel beállítása. Az adatok tudományos VM gyorsan állítható be, mert azt más rövid távú használati forgatókönyvek például a közzétett kísérleteket, bemutatók, online munkamenetet vagy konferencia oktatóanyagok a következő forgatókönyvek végrehajtása replikálása lehet alkalmazni.

### <a name="deep-learning"></a>A részletes tanulás
A virtuális gép adattudomány mély tanulási algoritmusok használata GPU (grafikus feldolgozóegység) alapú hardveren tanítási modell használható. Virtuális gép az Azure-felhőbe képességei skálázás okhoz, DSVM segít GPU-alapú hardveres használja a felhő szükség szerint. Egy váltani a GPU-alapú virtuális gépek nagy modell betanításakor, vagy a nagy sebességű számítások kell az azonos operációsrendszer-lemezképet megtartásával.  DSVM Windows Server 2016-kiadás előre előre telepített GPU-illesztőprogramokat, keretrendszerek és részletes tanulási keretrendszerek GPU verzióit. A Linux learning GPU mély a CentOS és az Ubuntu DSVMs engedélyezve van. Telepítheti az adatok tudományos VM Ubuntu, a CentOS, vagy a Windows 2016 kiadása nem GPU-alapú Azure virtuális gép ebben az esetben a mély tanulási keretrendszereket fog tartalék CPU módra. 

## <a name="whats-included-in-the-data-science-vm"></a>Az adatok tudományos VM tartalmát?
Az adatok tudományos virtuális gépnek számos népszerű adattudomány és mély tanulási eszközök telepítését és konfigurálását. Eszközök, amelyek megkönnyítik az Azure adatok és analitikák termékeivel is tartalmaz. A Microsoft ML Server (R, Python) használatával vagy az SQL Server 2017 nagy méretű adatkészletek prediktív modellek létrehozása, és fel. Az állomást, más eszközök és a Microsoft nyílt forráskódú közösségi egyaránt tartalmazza, valamint minta kódot és notebookok. Az alábbi táblázat részletezi, és összehasonlítja a Windows és Linux kiadásaiban az adatok tudományos virtuális gép fő összetevőit.


| **Eszköz**                                                           | **Windows Edition** | **Linux Edition** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R nyitott](https://mran.microsoft.com/open/) előtelepített népszerű csomagokkal   |I                      | I             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition tartalmazza, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) párhuzamos és elosztott nagy teljesítményű keretrendszer (R és Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) -a Microsoft új-az-a-legkorszerűbb ML algoritmusok <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R és Python Operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |I                      | I |
| [A Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) mellett plusz a megosztott aktiválási - Excel, a Word és a PowerPoint   |I                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, előre telepített népszerű csomagokkal 3.5    |I                      |I              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) előtelepített Ágnes nyelvhez népszerű csomagokkal                         |I                      |I              |
| Relációs adatbázisok                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(csak a CentOS) |
| Adatbázis-eszközök                                                       | * SQL Server Management Studio <br/>* Az SQL Server Integration Services<br/>* [BCP, Sqlcmd használatával](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC illesztőprogramok| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (eszköz lekérdezése), <br /> * bcp, Sqlcmd használatával <br /> * ODBC/JDBC illesztőprogramok|
| SQL Server ML-szolgáltatásokkal (R, Python) méretezhető adatbázis-elemzés | I     |N              |
| **[Jupyter Notebook Server](http://jupyter.org/) a következő mag,**                                  | I     | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | I | I |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | I (csak Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | I |
| JupyterHub (többfelhasználós notebookok kiszolgáló)| N | I |
| **Fejlesztői eszközök, IDEs és kód szerkesztők**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [A Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) > Git beépülő modul, az Azure HDInsight (Hadoop), a Data Lake, SQL Server Data tools [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs), és [Visual R eszközei Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim és Emacs | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* A Git és GitBash | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* A .net-keretrendszer | I | N |
| Power bi Desktop | I | N |
| SDK-k Azure és a szolgáltatások Cortana Intelligence Suite eléréséhez | I | I |
| **Adatmozgatást és a felügyeleti eszközök** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Az azure Storage Explorer | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure/overview) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Az azure Powershell | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [A BLOB biztosító illesztőprogram](https://github.com/Azure/azure-storage-fuse) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (az Azure Data Lake-tároló)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB adatáttelepítési eszköz](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [A Microsoft adatkezelési átjáró](https://msdn.microsoft.com/library/dn879362.aspx): adatok áthelyezése a helyi üzemeltetésű és a felhő között | I | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* A Unix/Linux rendszerű parancssori segédprogramok | I | I |
| [Apache részletezési](http://drill.apache.org) az adatok feltárása | I | I |
| **Machine Learning-eszközök** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integráció [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | I (csak Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | I (csak Ubuntu) |
| **Eszközök tanulási GPU-alapú mély** |Windows Server 2016 edition  | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft kognitív Toolkit (korábbi nevén CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | I | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | I | I|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow szolgál](https://www.tensorflow.org/serving/) | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, CUDNN, Nvidia illesztőprogram](https://developer.nvidia.com/cuda-toolkit) | I | I |
| **A big Data Platform (csak Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Helyi [Spark](http://spark.apache.org/) önálló | N | I |
| &nbsp;&nbsp;&nbsp;&nbsp;* Helyi [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | N | I |



## <a name="get-started-with-the-windows-data-science-vm"></a>Ismerkedjen meg a Windows Data tudományos VM
* Nyissa meg a kívánt Windows DSVM edition példányának létrehozása
  * [Windows Server 2016-alapú DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  vagy 
  * [Windows Server 2012-alapú DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Kattintson a **most az beszerzése informatikai** gombra.
* Jelentkezzen be a virtuális Gépet a virtuális gép létrehozása után a megadott hitelesítő adatok használatával a távoli asztalról.
* Fedezze fel, majd indítsa el az elérhető eszközöket, kattintson a **Start** menü.

## <a name="get-started-with-the-linux-data-science-vm"></a>Ismerkedés a Linux-adatok tudományos VM
* Nyissa meg a kívánt Linux DSVM edition példányának létrehozása 
  * [Ubuntu alapú DSVM](http://aka.ms/dsvm/ubuntu)

  vagy

  * [CentOS alapú DSVM](http://aka.ms/dsvm/centos)

  
* Kattintson a **most töltse le innen** gombra.
* Jelentkezzen be a virtuális gép egy SSH-ügyfél, például a Putty vagy SSH-parancs, a virtuális gép létrehozásakor megadott hitelesítő adatok használatával.
* A rendszerhéj parancssorában adja meg a dsvm-további-adatokat.
* Grafikus asztali, töltse le a saját ügyfélplatformjára X2Go ügyfél [Itt](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) és kövesse az utasításokat a Linux adatok tudományos VM dokumentumban [a Linux adatok tudományos virtuális gép kiépítéséhez](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>További lépések
### <a name="for-the-windows-data-science-vm"></a>A Windows Adattudomány virtuális gép számára
* Rendelkezésre álló eszközök futtatásához a Windows-verzión további információkért lásd: [a Microsoft Data tudományos virtuális gép kiépítéséhez](provision-vm.md) és
* Szükséges a Windows virtuális Gépre tudományos adatok projektjéhez tartozó különböző feladatok végrehajtásával kapcsolatos további információkért lásd: [tíz lehetősége van az adatok tudományos virtuális gép](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>A virtuális gép Linux Adattudomány
* A rendelkezésre álló eszközök futtatása a Linux-verzión további információkért lásd: [a Linux adatok tudományos virtuális gép kiépítéséhez](linux-dsvm-intro.md).
* Ez a forgatókönyv bemutatja, hogyan végezhető több közös adatok tudományos a Linux virtuális gép, lásd: [adattudomány lévő Linux adatok tudományos virtuálisgép](linux-dsvm-walkthrough.md).

