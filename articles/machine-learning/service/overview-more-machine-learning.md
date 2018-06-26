---
title: Egyéb gépi tanuláson alapuló Microsoft termékek – Azure Machine Learning | Microsoft Docs
description: Az Azure Machine Learning mellett számos egyéb lehetőség is rendelkezésre áll a Microsoft kínálatában a gépi tanulási modellek összeállítására, üzembe helyezésre és kezelésére.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 04/11/2018
ms.openlocfilehash: 20fd51f8906861e725b80202235e1684c31fd562
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268399"
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Más gépi tanuláson alapuló Microsoft-termékek és -szolgáltatások

Az [Azure Machine Learning](overview-what-is-azure-ml.md) mellett számos egyéb lehetőség is rendelkezésre áll a Microsoft kínálatában a gépi tanulási modellek összeállítására, üzembe helyezésre és kezelésére. 
* SQL Server Machine Learning-szolgáltatások
* Microsoft Machine Learning-kiszolgáló
* Azure Data Science Virtual Machine
* Spark MLLib a HDInsightban
* Batch AI Training szolgáltatás
* Microsoft Cognitive Toolkit (CNTK)
* Azure Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning-szolgáltatások
Az [SQL Server Microsoft Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) lehetővé teszik a gépi tanulási modellek R vagy Python használatával való futtatását, betanítását és üzembe helyezését. Helyszíni vagy SQL Server-adatbázisokban található adatokat is használhat. 

Használja a Microsoft Machine Learning-szolgáltatásokat, ha modelleket szeretne betanítani vagy üzembe helyezni a helyszínen vagy a Microsoft SQL Serverben. A Machine Learning-szolgáltatásokkal összeállított modellek az Azure Machine Learning Modellkezelővel helyezhetők üzembe. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló 
A [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) egy vállalati kiszolgáló, amely R- és Python-folyamatok párhuzamos és elosztott munkaterheléseinek futtatására és kezelésére szolgál. A Microsoft Machine Learning-kiszolgáló Linux és Windows rendszeren, valamint Hadoopon és Apache Sparkon fut. Ezenkívül elérhető a [HDInsighton](https://azure.microsoft.com/services/hdinsight/r-server/) is. Végrehajtómotorokat biztosít a [Microsoft Machine Learning-csomagokkal](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) összeállított megoldásokhoz, és kiterjeszti a nyílt forráskódú R és Python támogatását az alábbi forgatókönyvekre:

- Nagy teljesítményű elemzés
- Statisztikai elemzés
- Gépi tanulás
- Nagyméretű adatkészletek

A további funkciókat a kiszolgálóval telepített jogvédett csomagok biztosítják. Fejlesztéshez olyan IDE-ket használhat, mint például az [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) és a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Akkor érdemes a Microsoft Machine Learning-kiszolgálót használnia, ha:

- R vagy Python használatával összeállított modelleket szeretne összeállítani vagy üzembe helyezni egy kiszolgálón
- R- vagy Python-betanítást szeretne nagy méretben kiosztani egy Hadoop- vagy Spark-fürtön

## <a name="azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine
Az [Adatelemzési virtuális gép](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) egy személyre szabott virtuálisgép-környezet a Microsoft Azure-felhőben, amelyet kifejezetten adatelemzésre hoztak létre. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. Az Adatelemzési virtuális gép a Windows Server 2016-os és 2012-es verziójában, Linux rendszeren pedig az Ubuntu 16.04 LTS- és az OpenLogic CentOS 7.4-alapú disztribúciókon érhető el. 

Akkor érdemes az Adatelemzési virtuális gépet használnia, ha egyetlen csomóponton kell futtatnia a feladatait. Illetve ha távolról kell virtuálisan felskáláznia a feldolgozást egyetlen gépen. Az Azure Machine Learning-kísérletezés és az Azure Machine Learning Modellkezelés is célként támogatja az Adatelemzési virtuális gépet. 

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

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
A [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) egy egységesített mélytanulási eszközkészlet, amely számítási lépésekként írja le a neurális hálózatokat egy irányított gráfban. Ebben az irányított gráfban a levélcsomópontok a bemeneti értékeket vagy a hálózati paramétereket, míg az egyéb csomópontok a mátrixműveleteket jelölik a bemenetekkor. A Cognitive Toolkit segítségével egyszerűen létrehozhat és egyesíthet népszerű adatmodell-típusokat, például feed-forward DNN-eket, konvolúciós hálózatokat (CNN-ek), és ismétlődő hálózatokat (RNN-ek és LSTM-ek). Sztochasztikus gradiens módszeres (SGD, hiba-visszaterjesztés) tanulást hajt végre automatikus megkülönböztetéssel és párhuzamos folyamatkezeléssel több GPU-n és kiszolgálón.

A Cognitive Toolkitet mély tanulást használó modell összeállításához érdemes használnia.  A Cognitive Toolkit a korábbi szolgáltatások bármelyikében használható.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
Az [Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) egy nagyjából 30 API-ból álló készlet, amely lehetővé teszi a természetes kommunikációs módszereket használó alkalmazások összeállítását. Az API-k csupán néhány sornyi kód alapján lehetővé teszik az alkalmazások számára, hogy lássanak, halljanak, beszéljenek, megértsék és értelmezzék a felhasználó szükségleteit. Egyszerűen hozzáadhat intelligens funkciókat az alkalmazáshoz, például a következőket: 

- Érzelemfelismerés
- Látvány- és beszédfelismerés
- Language understanding (LUIS)
- Ismeretek és keresés

A Cognitive Services különböző eszközökre és platformokra történő alkalmazásfejlesztésre használható. Az API-k egyre fejlettebbek lesznek, és könnyű őket beállítani. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Machine Learning](overview-what-is-azure-ml.md) áttekintését.
