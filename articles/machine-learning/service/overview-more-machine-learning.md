---
title: Más gépi tanulási a termékeket a Microsoft - Azure Machine Learning |} Microsoft Docs
description: Azure Machine Learning kívül számos lehetőséget Microsoft létre, telepítheti és kezelheti a gépi tanulási modellek.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: bbb8deeab8368d9c0e6d29c8d7e1e2e0a8805d60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Egyéb machine learning termékek és szolgáltatások a Microsofttól

Az [Azure Machine Learning](overview-what-is-azure-ml.md) mellett számos egyéb lehetőség is rendelkezésre áll a Microsoft kínálatában a gépi tanulási modellek összeállítására, üzembe helyezésre és kezelésére. 
* SQL Server Machine Learning-szolgáltatások
* Microsoft Machine Learning-kiszolgáló
* Az Azure Data tudományos virtuális gép
* Spark MLLib a HDInsightban
* Batch AI Training szolgáltatás
* Microsoft kognitív Toolkit (CNTK)
* Az Azure kognitív szolgáltatások


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning-szolgáltatások
[SQL Server Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) lehetővé teszi a futtatásához, betanítását és központi telepítése a machine learning modellek R vagy Python. Helyszíni vagy SQL Server-adatbázisokban található adatokat is használhat. 

Használja a Microsoft Machine Learning-szolgáltatásokat, ha modelleket szeretne betanítani vagy üzembe helyezni a helyszínen vagy a Microsoft SQL Serverben. A Machine Learning-szolgáltatásokkal összeállított modellek az Azure Machine Learning Modellkezelővel helyezhetők üzembe. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló 
A [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) egy vállalati kiszolgáló, amely R- és Python-folyamatok párhuzamos és elosztott munkaterheléseinek futtatására és kezelésére szolgál. A Microsoft Machine Learning-kiszolgáló Linux és Windows rendszeren, valamint Hadoopon és Apache Sparkon fut. Ezenkívül elérhető a [HDInsighton](https://azure.microsoft.com/services/hdinsight/r-server/) is. Biztosít egy végrehajtó motorja használatával készített megoldások [Microsoft gépi tanulási csomagokat](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), és nyílt forráskódú R és Python bővíti a következő forgatókönyvek támogatása:

- Nagy teljesítményű elemzés
- Statisztikai elemzés
- Gépi tanulás
- Nagyméretű adatkészletek

További funkciókat, amelyek a kiszolgáló saját fejlesztésű csomagokon keresztül valósul meg. Fejlesztéshez olyan IDE-ket használhat, mint például az [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) és a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Akkor érdemes a Microsoft Machine Learning-kiszolgálót használnia, ha:

- R vagy Python használatával összeállított modelleket szeretne összeállítani vagy üzembe helyezni egy kiszolgálón
- R- vagy Python-betanítást szeretne nagy méretben kiosztani egy Hadoop- vagy Spark-fürtön

## <a name="azure-data-science-virtual-machine"></a>Az Azure Data tudományos virtuális gép
A [adatok tudományos virtuális gép](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) van a Microsoft Azure-felhő kifejezetten a adattudomány beépített testreszabott virtuálisgép-környezetet. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. Az adatok tudományos VM a Windows Server 2016 és 2012 verziók, és a Linux virtuális gép Ubuntu 16.04 LTS és OpenLogic CentOS 7.4-alapú terjesztési érhető el. 

Az adatok tudományos VM felhasználásával kell futtatni, vagy a feladatok egyetlen csomóponton üzemeltetni. Illetve ha távolról kell virtuálisan felskáláznia a feldolgozást egyetlen gépen. Az Azure Machine Learning-kísérletezés és az Azure Machine Learning Modellkezelés is célként támogatja az Adatelemzési virtuális gépet. 

## <a name="spark-mllib-in-hdinsight"></a>Spark MLLib a HDInsightban
A [Spark MLLib a HDInsightban](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) lehetővé teszi a modellek olyan Spark-feladatok részeként való létrehozását, amelyek Big Data típusú adatokon futnak. A Spark egyetlen feladatban lehetővé teszi adatok egyszerű átalakítását és előkészítését, majd horizontálisan felskálázható modellek létrehozását. A Spark MLLib segítségével létrehozott modellek üzembe helyezhetők, kezelhetők és monitorozhatók az Azure Machine Learning Modellkezelés segítségével. A betanítási futtatások továbbíthatók és kezelhetők az Azure Machine Learning-kísérletezéssel. A Spark a Machine Learning Workbenchben létrehozott adatelőkészítési feladatok horizontális felskálázására is használható. 

Akkor érdemes a Sparkot használnia, ha horizontálisan kell felskáláznia az adatfeldolgozást, valamint egy adatfolyamat részeként kell modelleket létrehoznia. Spark-feladatokat a következőkben hozhat létre: Scala, Java, Python vagy R. 

## <a name="batch-ai-training"></a>Batch AI Training 
Az [Azure Batch AI Training](https://aka.ms/batchaitraining) az AI-modellekkel párhuzamos kísérletezésben segít bármely keretrendszer használatakor, majd nagy számban betanítja őket csoportosított GPU-kon keresztül. Írja le a futtatandó feladat követelményeit és konfigurációját, a többit mi intézzük. 

A Batch AI Training lehetővé teszi mélytanulási feladatok horizontális felskálázását csoportosított GPU-k között, például az alábbi keretrendszerek használatával:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Az Azure Machine Learning Modellkezelés használható modellek átvételére a Batch AI Trainingtől a modellek üzembe helyezése, kezelése és monitorozása céljából.  A Batch AI Training később integrálva lesz az Azure Machine Learning-kísérletezésbe. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft kognitív Toolkit (CNTK)
A [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) egy egységesített mélytanulási eszközkészlet, amely számítási lépésekként írja le a neurális hálózatokat egy irányított gráfban. Ebben az irányított gráfban a levélcsomópontok a bemeneti értékeket vagy a hálózati paramétereket, míg az egyéb csomópontok a mátrixműveleteket jelölik a bemenetekkor. A Cognitive Toolkit segítségével egyszerűen létrehozhat és egyesíthet népszerű adatmodell-típusokat, például feed-forward DNN-eket, konvolúciós hálózatokat (CNN-ek), és ismétlődő hálózatokat (RNN-ek és LSTM-ek). Sztochasztikus gradiens módszeres (SGD, hiba-visszaterjesztés) tanulást hajt végre automatikus megkülönböztetéssel és párhuzamos folyamatkezeléssel több GPU-n és kiszolgálón.

A Cognitive Toolkitet mély tanulást használó modell összeállításához érdemes használnia.  A Cognitive Toolkit a korábbi szolgáltatások bármelyikében használható.

## <a name="azure-cognitive-services"></a>Az Azure kognitív szolgáltatások
[Azure-szolgáltatások kognitív](https://docs.microsoft.com/azure/#pivot=products&panel=ai) körülbelül 30 készlete API-k, amelyek lehetővé teszik a kommunikáció természetes módszerek használó alkalmazások készítéséhez. Ezen API-k biztosíthatja a alkalmazás lásd, hall, beszéd, megértéséhez, és pár sornyi kód rendelkező felhasználónak rendelkeznie kell értelmezni. Egyszerűen hozzáadhat intelligens funkciókat az alkalmazáshoz, például a következőket: 

- Érzelemfelismerés
- Látvány- és beszédfelismerés
- Nyelvi ismertetése (LUIS)
- Ismeretek és keresés

Kognitív szolgáltatások segítségével hogyan fejleszthetnek alkalmazásokat az eszközökön és platformokat. Az API-k egyre fejlettebbek lesznek, és könnyű őket beállítani. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Machine Learning](overview-what-is-azure-ml.md) áttekintését.
