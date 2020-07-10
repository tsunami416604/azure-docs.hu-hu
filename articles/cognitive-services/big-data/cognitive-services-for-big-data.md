---
title: Cognitive Services Big-adatmennyiséghez
description: Ismerje meg, hogyan használhatja az Azure Cognitive Servicest nagyméretű adatkészleteken a Python, a Java és a Scala használatával. A Big adatok Cognitive Services a folyamatosan fejleszthető, intelligens modellek közvetlenül Apache Spark &trade; és SQL-számításokban is beágyazható.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 0001002636362a74915f9dae21a6beff52baea63
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201779"
---
# <a name="azure-cognitive-services-for-big-data"></a>Azure-Cognitive Services Big-adatmennyiségekhez

![Azure-Cognitive Services Big-adatmennyiségekhez](media/cognitive-services-big-data-overview.svg)

A Big User Azure-Cognitive Services lehetővé teszi a felhasználók számára, hogy a [Apache Spark &trade; ](https://docs.microsoft.com/dotnet/spark/what-is-spark)használatával Cognitive Services az adatcsatornákat. A Big-alapú adatCognitive Servicesek esetében könnyedén létrehozhat nagy méretű intelligens alkalmazásokat bármilyen adattárral.

A Big adatok Cognitive Services a folyamatosan fejleszthető, intelligens modellek közvetlenül Apache Spark &trade; és SQL-számításokban is beágyazható. Ezek az eszközök felszabadítják a fejlesztőket az alacsony szintű hálózatkezelési részletektől, hogy az intelligens, elosztott alkalmazások létrehozására összpontosítsanak.

## <a name="features-and-benefits"></a>Funkciók és előnyök

A Big-adatok Cognitive Services a világ bármely régiójában, valamint a [tároló-Cognitive Servicesban](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)is használhatnak szolgáltatásokat. A tárolók alacsony késleltetésű válaszokkal támogatják az alacsony vagy a kapcsolat nélküli központi telepítéseket. A tároló Cognitive Services futtathatók helyileg, közvetlenül a Spark-fürt munkavégző csomópontjain vagy egy külső Orchestrator, például a Kubernetes.

## <a name="supported-services"></a>Támogatott szolgáltatások

Az API-kkal és SDK-kkal elért [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)segítségével a fejlesztők intelligens alkalmazásokat hozhatnak létre anélkül, hogy AI-vagy adatelemzési ismeretekkel rendelkeznek. A Cognitive Services segítségével megtekintheti az alkalmazásait, meghallgathatja, elolvashatja, megértette és indoklását. Az Cognitive Services használatához az alkalmazásnak a hálózaton keresztül kell küldenie az adatait a szolgáltatásnak. A fogadás után a szolgáltatás intelligens választ küld vissza. Big data munkaterhelésekhez a következő szolgáltatások érhetők el:

### <a name="vision"></a>Látás

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Számítógépes látástechnológia](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Számítógépes látástechnológia")| A Computer Vision szolgáltatás a rendszerképek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális algoritmusokhoz. |
|[Arcfelismerés](https://docs.microsoft.com/azure/cognitive-services/face/ "Arcfelismerés")| A Face szolgáltatás hozzáférést biztosít a speciális arc-algoritmusokhoz, lehetővé téve a Face attribútumok észlelését és felismerését. |

### <a name="speech"></a>Beszéd

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Speech szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech szolgáltatás")|A beszédfelismerési szolgáltatás hozzáférést biztosít olyan szolgáltatásokhoz, mint a beszédfelismerés, a beszédfelismerés, a beszédfelismerés és a hangszórók ellenőrzése és azonosítása.|

### <a name="decision"></a>Döntés

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomáliadetektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomáliadetektor") | Az anomália-detektor (előzetes verzió) szolgáltatás lehetővé teszi, hogy az idősorozat-adataiban figyelje és azonosítsa a rendellenességeket.|

### <a name="language"></a>Nyelv

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Szövegelemzés")| A Text Analytics szolgáltatás természetes nyelvi feldolgozást tesz lehetővé a nyers szövegekben az érzelmek elemzése, a kulcs-kifejezés kinyerése és a nyelvfelismerés terén.|

### <a name="search"></a>Keresés

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing – Képkeresés](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing – Képkeresés")|A Bing Image Search szolgáltatás visszaadja a felhasználó lekérdezéséhez szükséges képek megjelenítését.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Támogatott programozási nyelvek a Big-alapú adatCognitive Servicesokhoz

A Big-adatCognitive Services a Apache Sparkra épül. A Apache Spark egy olyan elosztott számítástechnikai könyvtár, amely támogatja a Java, a Scala, a Python, az R és számos más nyelvet. Ezek a nyelvek jelenleg támogatottak.

### <a name="python"></a>Python

PySpark API-t biztosítunk a `mmlspark.cognitive` [Microsoft ml Apache Spark](https://aka.ms/spark)-beli névterében. További információ: [Python fejlesztői API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Használati példákért tekintse meg a [Python-mintákat](samples-python.md).

### <a name="scala-and-java"></a>Scala és Java

Egy Scala és Java-alapú Spark API-t biztosítunk a `com.microsoft.ml.spark.cognitive` [Microsoft ml Apache Spark](https://aka.ms/spark)-beli névterében. További információkért lásd a [Scala fejlesztői API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package)-t. Használati példákért tekintse meg a [Scala-mintákat](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Támogatott platformok és összekötők

A Big for típusú adatCognitive Serviceshoz Apache Spark szükséges. Több Apache Spark platform is van, amelyek támogatják a Big-adatCognitive Servicest.

### <a name="azure-databricks"></a>Azure Databricks

Az [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) a Microsoft Azure Cloud Services platformra optimalizált Apache Spark-alapú elemzési platform. Egy kattintással üzembe helyezést, gördülékeny munkafolyamatokat és interaktív munkaterületet biztosít, amely támogatja az adatszakértők, az adatmérnökök és az üzleti elemzők közötti együttműködést.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Az [Azure szinapszis Analytics](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/synapse-analytics) (korábbi nevén SQL Data Warehouse) olyan vállalati adattárház, amely nagy mértékben párhuzamos feldolgozást használ. A szinapszis Analytics segítségével gyorsan futtathat összetett lekérdezéseket az petabájt között. Az Azure szinapszis Analytics felügyelt Spark-készleteket biztosít a Spark-feladatok intuitív Jupyter Notebook felülettel való futtatásához.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az [Azure Kubernetes Service (ak)](https://docs.microsoft.com/azure/aks/) nagy léptékű Docker-tárolókat és elosztott alkalmazásokat szervez. Az AK egy felügyelt Kubernetes-ajánlat, amely leegyszerűsíti a Kubernetes használatát az Azure-ban. A Kubernetes lehetővé teszi a kognitív szolgáltatások skálázásának, késésének és hálózatkezelésének részletes szabályozását. Ha azonban nem ismeri a Apache Spark használatát, javasoljuk, hogy használjon Azure Databricks vagy szinapszis-elemzést.

### <a name="data-connectors"></a>Adatösszekötők

Ha már rendelkezik Spark-fürttel, a következő lépés az adataihoz csatlakozik. A Apache Spark adatbázis-összekötők széles gyűjteményét kínálja. Ezek az összekötők lehetővé teszik, hogy az alkalmazások nagyméretű adatkészletekkel működjenek, függetlenül attól, hogy hol vannak tárolva. A támogatott adatbázisokkal és összekötővel kapcsolatos további információkért tekintse [meg a Azure Databricks támogatott adatforrások listáját](https://docs.microsoft.com/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Alapelvek

### <a name="spark"></a>Spark

[Apache Spark &trade; ](http://spark.apache.org/) a egy egységes analitikai motor a nagyméretű adatfeldolgozáshoz. A parallel processing Framework big data-és analitikai alkalmazások teljesítményét fokozza. A Spark egy batch-és adatfolyam-feldolgozó rendszerként is működhet, az alapszintű alkalmazás kódjának módosítása nélkül.

A Spark alapja a DataFrame: a Apache Spark munkavégző csomópontok között elosztott adattáblázatos gyűjtemény. A Spark-DataFrame olyanok, mint egy táblázat egy rokon adatbázisban, vagy egy R/Python adatkeretben, de korlátlan léptékben. A DataFrames számos forrásból lehet létrehozni, például: strukturált adatfájlok, struktúra táblái vagy külső adatbázisok. Ha az adatai Spark-DataFrame, a következőket teheti:

   - SQL-stílusú számítások, például csatlakozás és szűrési táblák.
   - Függvények alkalmazása nagyméretű adatkészletekre a MapReduce stílusú párhuzamosságok használatával.
   - Elosztott Machine Learning alkalmazása a Apache Spark Microsoft Machine Learning használatával.
   - A nagy mennyiségű adatCognitive Services a használatra kész intelligens szolgáltatásokkal gazdagíthatja adatait.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning for Apache Spark (MMLSpark)

A [Microsoft Machine learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) egy Apache Spark-ra épülő, nyílt forráskódú, elosztott gépi tanulási könyvtár (ml). A nagyméretű adatCognitive Services a csomagban szerepel. Emellett a MMLSpark számos más ML-eszközt is tartalmaz a Apache Sparkhoz, például LightGBM, Vowpal Wabbit, OpenCV, mész és sok más. A MMLSpark segítségével hatékony prediktív és analitikai modelleket hozhat létre bármely Spark-adatforrásból.

### <a name="http-on-spark"></a>HTTP a Sparkon

A Big-alapú adatCognitive Services példa arra, hogy miként integrálható az intelligens webszolgáltatások big data használatával. A webszolgáltatások számos különböző alkalmazást a világon, a legtöbb szolgáltatás pedig a Hypertext Transfer Protocol (HTTP) keresztül kommunikál. Ha *tetszőleges* webszolgáltatásokkal szeretne dolgozni, nagy léptékben biztosítunk http-t a sparkon. A HTTP on Spark használatával bármilyen webszolgáltatáson át lehet adni terabájt adatmennyiséget. A motorháztető alatt ezt a technológiát használjuk a Big adathoz való Cognitive Services.

## <a name="developer-samples"></a>Fejlesztői minták

- [Recept: prediktív karbantartás](recipes/anomaly-detection.md)
- [Recept: intelligens művészeti feltárás](recipes/art-explorer.md)

## <a name="blog-posts"></a>Blogbejegyzések

- [További információ a Cognitive Services Work on Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Snow leopárdok mentése mély tanulással és Computer Vision a Sparkban](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research Podcast: MMLSpark, a jó AI-t a Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)

## <a name="webinars-and-videos"></a>Webes előadások és videók

- [Az Azure Cognitive Services a Sparkon: fürtök beágyazott intelligens szolgáltatásokkal](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark Summit alaphang: skálázható AI jó](https://databricks.com/session_eu19/scalable-ai-for-good)
- [A Big Cosmos DB Cognitive Services](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)

## <a name="next-steps"></a>További lépések

- [Első lépések a Big adattal rendelkező Cognitive Services](getting-started.md)
- [Egyszerű Python-példák](samples-python.md)
- [Egyszerű Scala-példák](samples-scala.md)
