---
title: Ismerkedjen meg Cognitive Services Big-beli adatkezeléssel
description: Állítsa be a MMLSpark folyamatot a Cognitive Services Azure Databricks és futtasson egy mintát.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 5256db4547f8c19960a6f470ce3c84b2eef25e24
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776612"
---
# <a name="getting-started"></a>Első lépések

A környezet beállítása az első lépés egy folyamat létrehozásához az adataihoz. Miután a környezet elkészült, a minta futtatása gyorsan és egyszerűen elvégezhető.

Ebben a cikkben a következő lépések végrehajtásával kezdheti meg az első lépéseket:

1. [Cognitive Services-erőforrás létrehozása](#create-a-cognitive-services-resource)
1. [Apache Spark-fürt létrehozása](#create-an-apache-spark-cluster)
1. [Minta kipróbálása](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

A Big Cognitive Services használatához először létre kell hoznia egy kognitív szolgáltatást a munkafolyamathoz. A Cognitive Services két fő típusa létezik: az Azure-ban üzemeltetett felhőalapú szolgáltatások és a felhasználók által kezelt tároló szolgáltatások. Javasoljuk, hogy kezdje az egyszerűbb felhőalapú Cognitive Services.

### <a name="cloud-services"></a>Felhőszolgáltatások

A felhőalapú Cognitive Services az Azure-ban üzemeltetett intelligens algoritmusok. Ezek a szolgáltatások a képzés nélkül is használatra készen állnak, csak internetkapcsolatra van szükség. A Azure Portal vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows)használatával [hozhat létre kognitív szolgáltatást](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows) .

### <a name="containerized-services-optional"></a>Tároló szolgáltatások (nem kötelező)

Ha az alkalmazás vagy a számítási feladat rendkívül nagy adatkészleteket használ, magánhálózat szükséges, vagy nem tud kapcsolatba lépni a felhővel, előfordulhat, hogy a felhőalapú szolgáltatásokkal folytatott kommunikáció nem lehetséges. Ebben az esetben a tároló Cognitive Services a következő előnyökkel jár:

* **Alacsony kapcsolat**: a tárolók Cognitive Services üzembe helyezése bármely számítástechnikai környezetben, a felhőben és a szolgáltatáson kívül is. Ha az alkalmazás nem tud kapcsolatba lépni a felhővel, vegye fontolóra a tárolók Cognitive Services telepítését az alkalmazáson.

* **Kis késés**: mivel a tároló szolgáltatások nem igénylik a felhőbe irányuló kétirányú kommunikációt, a válaszok sokkal alacsonyabb késéssel lesznek visszaadva.

* **Adatvédelem és**adatvédelem: a tárolón keresztüli szolgáltatásokat központilag telepítheti a hálózatba, így a bizalmas adatok nem hagyják el a hálózatot.

* **Magas skálázhatóság**: a tároló szolgáltatások nem rendelkeznek a "díjszabási korlátokkal", és a felhasználó által felügyelt számítógépeken futnak. Így a Cognitive Services méretezés nélkül is méretezhető, így sokkal nagyobb számítási feladatokat kezelhet.

Kövesse [ezt az útmutatót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support?tabs=luis) egy tárolós kognitív szolgáltatás létrehozásához.

## <a name="create-an-apache-spark-cluster"></a>Apache Spark-fürt létrehozása

[Apache Spark &trade; ](http://spark.apache.org/) a egy elosztott számítástechnikai keretrendszer, amely nagy adatfeldolgozást tesz lehetővé. A felhasználók az Azure-ban Apache Sparkkal dolgozhatnak olyan szolgáltatásokkal, mint a Azure Databricks, az Azure szinapszis Analytics, a HDInsight és az Azure Kubernetes Services. A Big Cognitive Services használatához először létre kell hozni egy fürtöt. Ha már rendelkezik Spark-fürttel, próbálkozzon egy példával.

### <a name="azure-databricks"></a>Azure Databricks

A Azure Databricks egy Apache Spark-alapú elemzési platform, amely egy kattintással beállítható, áramvonalas munkafolyamatokkal és interaktív munkaterülettel rendelkezik. Gyakran használják az adatszakértők, a mérnökök és az üzleti elemzők közötti együttműködésre. Ha a Big adatCognitive Servicest szeretné használni a Azure Databrickson, kövesse az alábbi lépéseket:

1. [Azure Databricks-munkaterület létrehozása](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Spark-fürt létrehozása a Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Nagy mennyiségű adatCognitive Services telepítése
    * Új könyvtár létrehozása a databricks-munkaterületen  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Adja meg a következő Maven-koordináták koordinátáit:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` adattár: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * A könyvtár telepítése fürtre  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="synapse-analytics-optional"></a>Szinapszis Analytics (nem kötelező)

A szinapszis Analytics használatával Spark-fürtöt is létrehozhat. Az Azure szinapszis Analytics egyesíti a vállalati adattárházat és a big data elemzéseket. Lehetővé teszi, hogy a feltételek alapján lekérdezzen egy kiszolgáló nélküli igény szerinti vagy kiépített erőforrást a skálán. A szinapszis Analytics használatának megkezdéséhez kövesse az alábbi lépéseket:

1. [Hozzon létre egy szinapszis-munkaterületet (előzetes verzió)](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace).
1. [Hozzon létre egy új Apache Spark készletet (előzetes verzió) a Azure Portal használatával](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-portal).

A szinapszis Analyticsben alapértelmezés szerint a Cognitive Services Big default típusú adatmennyiség van telepítve.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Ha tárolón alapuló Cognitive Services használ, a Spark mellett a tárolók egyik népszerű beállítása az Azure Kubernetes szolgáltatás.

Az Azure Kubernetes szolgáltatás megkezdéséhez kövesse az alábbi lépéseket:

1. [Azure Kubernetes Service (ak) fürt üzembe helyezése a Azure Portal használatával](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
1. [A Apache Spark 2.4.0 Helm-diagram telepítése](https://hub.helm.sh/charts/microsoft/spark)
1. [Kognitív szolgáltatás tárolójának telepítése a Helm használatával](https://docs.microsoft.com/azure/cognitive-services/computer-vision/deploy-computer-vision-on-premises)

## <a name="try-a-sample"></a>Minta kipróbálása

A Spark-fürt és-környezet beállítása után rövid mintát is futtathat. Ez a szakasz azt mutatja be, hogyan használható a Big Cognitive Services a Azure Databricksban.

Először is létrehozhatunk egy jegyzetfüzetet Azure Databricks. Más Spark-fürtszolgáltatások esetén használja a notebookot vagy a Spark-küldést.

1. Hozzon létre egy új Databricks-jegyzetfüzetet az **Azure Databricks** menüben az **új jegyzetfüzet** lehetőség kiválasztásával.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. A **Jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy nevet, válassza ki a **Python** nyelvet, és válassza ki a korábban létrehozott Spark-fürtöt.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Kattintson a **Létrehozás** gombra.

1. Illessze be az alábbi kódrészletet az új jegyzetfüzetbe.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Szerezze be az előfizetési kulcsot a Azure Portal Text Analytics irányítópultjának **kulcsok és végpont** menüjéből.
1. Cserélje le az előfizetési kulcs helyőrzőjét a Databricks notebook kódjába az előfizetési kulcsával.
1. A minta futtatásához válassza ki a Play (lejátszás) vagy a háromszög, a jegyzetfüzet cellájának jobb felső sarkában található szimbólumot. Ha szeretné, az összes cella futtatásához válassza az **összes futtatása** lehetőséget a jegyzetfüzet tetején. A válaszok a táblázat cellái alatt jelennek meg.

### <a name="expected-results"></a>Várt eredmények

| szöveg                                      |   hangulatelemzés |
|:------------------------------------------|------------:|
| Boldog vagyok, ma, a napsütötte!           |   0,978959  |
| Ez a Rush Hour-forgalom csalódott vagyok. |   0,0237956 |
| A sparkon nem rossz a kognitív szolgáltatások  |   0,888896  |

## <a name="next-steps"></a>Következő lépések

- [Rövid Python-példák](samples-python.md)
- [Rövid Scala-példák](samples-scala.md)
- [Recept: prediktív karbantartás](recipes/anomaly-detection.md)
- [Recept: intelligens művészeti feltárás](recipes/art-explorer.md)
