---
title: 'Oktatóanyag: anomáliák észlelése Cognitive Services'
description: Oktatóanyag a Cognitive Services kihasználása a szinapszisok észleléséhez
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468258"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Oktatóanyag: anomáliák észlelése Cognitive Servicessal (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan gazdagíthatja adatait az Azure Szinapszisban [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492)segítségével. A anomáliák észleléséhez az [anomália detektort](https://go.microsoft.com/fwlink/?linkid=2147493) fogjuk használni. Az Azure szinapszis egyik felhasználója egyszerűen kiválaszthat egy táblázatot, amely gazdagabbá teszi a rendellenességek észlelését.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> - Az idősorozat-adatokat tartalmazó Spark Table-adatkészlet beszerzésének lépései.
> - Az Azure Szinapszisban az adatelemzési szolgáltatásokkal gazdagíthatja az adatvesztést a varázsló használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A ADLS Gen2-fájlrendszer **Storage blob-Adatközreműködőinek** kell lennie.
- Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Spark-készlet létrehozása az Azure szinapszisban](../quickstart-create-sql-pool-studio.md).
- Az oktatóanyag használata előtt el kell végeznie az oktatóanyagban ismertetett előzetes konfigurálási lépéseket is. [Cognitive Services konfigurálása az Azure szinapszisban](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Spark-tábla létrehozása

Ehhez az oktatóanyaghoz szüksége lesz egy Spark-táblázatra.

1. Töltse le a következő, kódot tartalmazó jegyzetfüzet-fájlt egy Spark-táblázat létrehozásához: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Töltse fel a fájlt az Azure szinapszis-munkaterületére.
![Jegyzetfüzet feltöltése](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Nyissa meg a jegyzetfüzet-fájlt, és válassza az **összes cella futtatása** lehetőséget.
![Spark-tábla létrehozása](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Egy **anomaly_detector_testing_data** nevű Spark-táblázatnak ekkor az alapértelmezett Spark-adatbázisban kell megjelennie.

## <a name="launch-cognitive-services-wizard"></a>Cognitive Services elindítása varázsló

1. Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblára. A varázsló megnyitásához válassza a "Machine Learning-> gazdagabbá a meglévő modellel" lehetőséget.
![Pontozási varázsló indítása](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Ekkor megjelenik egy konfigurációs panel, és a rendszer kérni fogja, hogy válasszon ki egy Cognitive Services modellt. Válassza ki a anomália detektort.

![Pontozási varázsló indítása – 1. lépés](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Hitelesítő adatok megadása

A Cognitive Services való hitelesítéshez hivatkoznia kell a titkos kulcsra, hogy az a Key Vault használja. Az alábbi bemenetek attól függően változnak, hogy milyen [lépéseket](tutorial-configure-cognitive-services-synapse.md) kell végrehajtania a lépés előtt.

- **Azure-előfizetés**: válassza ki azt az Azure-előfizetést, amelyhez a Key Vault tartozik.
- **Cognitive Services fiók**: ez az a Text Analytics erőforrás, amelyhez csatlakozni fog.
- **Azure Key Vault társított szolgáltatás**: Az előfeltételként szükséges lépések részeként létrehozott egy társított szolgáltatást a Text Analytics-erőforráshoz. Adja meg itt.
- **Titok neve**: Ez annak a kulcsnak a neve, amely a Key vaultban a Cognitive Services-erőforráshoz való hitelesítés kulcsát tartalmazza.

![Azure Key Vault részleteinek megadása](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Anomáliák észlelésének konfigurálása

Ezután konfigurálnia kell a anomáliák észlelését. Adja meg a következő adatokat:

- Részletesség: az adatmintavételezési arány. Ha például az adatai percenként értékkel rendelkeznek, akkor a részletessége percben fog megjelenni. **Havonta** válassza 

- Időbélyeg: az adatsorozat időpontját jelképező oszlop. Oszlop **időbélyegének** kiválasztása

- Idősor érték: az adatsorozat értékét jelképező oszlop, amely az időbélyegző oszlopban megadott időpontot jelöli. Oszlop **értékének** kiválasztása

- Csoportosítás: az adatsorozatot csoportosító oszlop. Ez azt eredményezi, hogy az ebben az oszlopban szereplő összes sornak egy idősorozatot kell alkotnia. **Oszlopcsoport kiválasztása**

Ha elkészült, válassza a **Jegyzetfüzet megnyitása** lehetőséget. Ezzel létrehoz egy jegyzetfüzetet a PySpark-kóddal, amely az Azure Cognitive Services használatával anomália-észlelést végez.

![Anomália-detektor konfigurálása](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Jegyzetfüzet és Futtatás megnyitása

Az imént megnyitott jegyzetfüzet a [mmlspark könyvtárat](https://github.com/Azure/mmlspark) használja a kognitív szolgáltatásokhoz való kapcsolódáshoz.

A megadott Azure Key Vault adatok lehetővé teszik, hogy a tapasztalatok alapján biztonságosan hivatkozzon a titkos kulcsokra.

Mostantól az **összes cellát futtathatja** a anomáliák észlelésének elvégzéséhez. További információ a [Cognitive Services-anomália detektorról](https://go.microsoft.com/fwlink/?linkid=2147493).

![Anomáliák észlelésének futtatása](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: érzelmek elemzése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis dedikált SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md)
- [Az Azure szinapszis Analytics Machine Learning képességei](what-is-machine-learning.md)