---
title: 'Oktatóanyag: érzelmek elemzése Cognitive Services'
description: Oktatóanyag a Cognitive Services for szentimentális-elemzéshez a Szinapszisban
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468615"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Oktatóanyag: érzelmek elemzése a Cognitive Services (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan gazdagíthatja adatait az Azure Szinapszisban [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492)segítségével. A [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) képességeket fogjuk használni az érzelmek elemzéséhez. Az Azure szinapszis egyik felhasználója egyszerűen kijelölhet egy olyan táblázatot, amely az érzelmekkel gazdagított szöveges oszlopot tartalmaz. Ezek az érzelmek pozitívak, negatívak, vegyesek vagy semlegesek is lehetnek, és a rendszer a valószínűséget is visszaadja.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> - A szöveg oszlopot tartalmazó Spark Table-adatkészlet beszerzésének lépései az érzelmek elemzéséhez.
> - Az Azure Szinapszisban az adatText Analytics-Cognitive Services használatával gazdagíthatja az adatgyűjtést a varázsló segítségével.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A ADLS Gen2-fájlrendszer **Storage blob-Adatközreműködőinek** kell lennie.
- Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Spark-készlet létrehozása az Azure szinapszisban](../quickstart-create-sql-pool-studio.md).
- Az oktatóanyag használata előtt el kell végeznie az oktatóanyagban ismertetett előzetes konfigurálási lépéseket is. [Cognitive Services konfigurálása az Azure szinapszisban](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Spark-tábla létrehozása

Ehhez az oktatóanyaghoz szüksége lesz egy Spark-táblázatra.

1. Töltse le a következő CSV-fájlt, amely a Text Analytics adatkészletét tartalmazza: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Töltse fel a fájlt az Azure szinapszis ADLSGen2 Storage-fiókjába.
![Adatok feltöltése](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Hozzon létre egy Spark-táblázatot a. csv fájlból úgy, hogy jobb gombbal a fájlra kattint, és kiválasztja az **új jegyzetfüzet – > Spark-tábla létrehozása** lehetőséget.
![Spark-tábla létrehozása](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nevezze el a táblát a Code (kód) cellában, és futtassa a jegyzetfüzetet egy Spark-készleten. Ne felejtse el beállítani a "header = true" értéket.
![Jegyzetfüzet futtatása](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Cognitive Services elindítása varázsló

1. Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblára. A varázsló megnyitásához válassza a "Machine Learning-> gazdagabbá a meglévő modellel" lehetőséget.
![Pontozási varázsló indítása](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Ekkor megjelenik egy konfigurációs panel, és a rendszer kérni fogja, hogy válasszon ki egy Cognitive Services modellt. Válassza a Text Analytics – Hangulatelemzés elemet.

![Pontozási varázsló indítása – 1. lépés](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Hitelesítő adatok megadása

A Cognitive Services való hitelesítéshez hivatkoznia kell a titkos kulcsra, hogy az a Key Vault használja. Az alábbi bemenetek attól függően változnak, hogy milyen [lépéseket](tutorial-configure-cognitive-services-synapse.md) kell végrehajtania a lépés előtt.

- **Azure-előfizetés**: válassza ki azt az Azure-előfizetést, amelyhez a Key Vault tartozik.
- **Cognitive Services fiók**: ez az a Text Analytics erőforrás, amelyhez csatlakozni fog.
- **Azure Key Vault társított szolgáltatás**: Az előfeltételként szükséges lépések részeként létrehozott egy társított szolgáltatást a Text Analytics-erőforráshoz. Adja meg itt.
- **Titok neve**: Ez annak a kulcsnak a neve, amely a Key vaultban a Cognitive Services-erőforráshoz való hitelesítés kulcsát tartalmazza.

![Azure Key Vault részleteinek megadása](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Hangulatelemzés konfigurálása

Ezután konfigurálnia kell a hangulat elemzését. Adja meg a következő adatokat:
- **Nyelv**: válassza ki annak a szövegnek a nyelvét, amelynek az elemzését el szeretné végezni. Válassza az **en** lehetőséget.
- **Text (szöveg) oszlop**: ez az adatkészlet azon szöveges oszlopa, amelyet elemezni kíván, hogy meghatározza a véleményét. Válassza a táblázat oszlop **megjegyzése** elemet.

Ha elkészült, kattintson a **Jegyzetfüzet megnyitása** lehetőségre. Ez létrehoz egy jegyzetfüzetet az Ön számára az PySpark-kóddal, amely elvégzi az érzelmek elemzését az Azure Cognitive Services.

![Hangulatelemzés konfigurálása](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Jegyzetfüzet és Futtatás megnyitása

Az imént megnyitott jegyzetfüzet a [mmlspark könyvtárat](https://github.com/Azure/mmlspark) használja a kognitív szolgáltatásokhoz való kapcsolódáshoz.

A megadott Azure Key Vault adatok lehetővé teszik, hogy a tapasztalatok alapján biztonságosan hivatkozzon a titkos kulcsokra.

Mostantól az **összes** cella használatával gazdagíthatja adatait az érzelmekkel. Az érzelmek pozitív/negatív/semleges/vegyes értékként lesznek visszaadva, és a rendszer az egyes érzelmeket is felhasználja. További információ a [Cognitive Services-hangulat elemzéséről](https://go.microsoft.com/fwlink/?linkid=2147792).

![Hangulatelemzés futtatása](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>További lépések
- [Oktatóanyag: anomáliák észlelése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis dedikált SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md)
- [Az Azure Azure szinapszis Analytics Machine Learning képességei](what-is-machine-learning.md)