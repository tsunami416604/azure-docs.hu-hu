---
title: Mi az az Anomaly Detector API?
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API speciális algoritmusait használva azonosíthatja az idősorozat-adataiban mutatkozó rendellenességeket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3bd60ff1e732940bbb13c2e224084cf7e331266b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934862"
---
# <a name="what-is-the-anomaly-detector-api"></a>Mi az az Anomaly Detector API?

A rendellenesség-Kiderítő API lehetővé teszi, hogy az idősoros adataiban a gépi tanulással figyelje és azonosítsa a rendellenességeket. Az anomália-érzékelő API úgy alkalmazkodik, hogy automatikusan azonosítja és alkalmazza a legjobban illeszkedő modelleket az adataiba, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől. Az idősorozat-adatok használatával az API meghatározza a rendellenességek észlelésére, a várt értékekre vonatkozó határokat, és hogy mely adatpontok rendellenesek.

![A szolgáltatási kérelmekben szereplő minták változásainak észlelése](./media/anomaly_detection2.png)

Az anomália detektor használata nem igényel korábbi felhasználói élményt a gépi tanulásban, és a REST API lehetővé teszi a szolgáltatás egyszerű integrálását az alkalmazásokba és folyamataiba.

## <a name="features"></a>Szolgáltatások

Az anomáliák detektorral automatikusan észlelheti a rendellenességeket az idősorozat-adataiban, vagy valós időben végezheti el azokat. 

|Funkció  |Leírás  |
|---------|---------|
|A valós időben előforduló rendellenességek észlelése. | A korábban látott adatpontok használatával észlelheti a folyamatos adatátviteli hibák észlelését, és meghatározhatja, hogy a legújabb-e anomália. A művelet létrehoz egy modellt a küldött adatpontok használatával, és meghatározza, hogy a célként megadott pont anomáli-e. Ha az API-t az Ön által létrehozott összes új adatpontra hívja fel, az adatait a létrehozásuk során figyelheti. |
|Az adatkészletekben lévő rendellenességek észlelése kötegként. | Az idősorozat használatával észlelheti az összes olyan rendellenességet, amely létezhet az összes adategységben. A művelet létrehoz egy modellt a teljes idősorozat-adataival, és minden olyan pontot, amely ugyanazzal a modellel lett elemezve.         |
| További információkat kaphat az adatairól. | Hasznos információkhoz juthat az adatokról és az észlelt rendellenességekről, beleértve a várt értékeket, a rendellenességek határait és a pozíciókat. |
| Állítsa be a anomália észlelési határait. | Az anomália detektor API automatikusan létrehoz határokat a anomáliák észleléséhez. Állítsa be ezeket a határokat az API-k adatrendellenességekre való érzékenységének növeléséhez vagy csökkentéséhez, és jobban illeszkedjen az adataihoz. |

## <a name="demo"></a>Demó

Tekintse meg ezt az [interaktív bemutatót](https://aka.ms/adDemo) , hogy megtudja, hogyan működik a rendellenesség-érzékelő.
A bemutató futtatásához létre kell hoznia egy anomália-detektor erőforrást, és le kell kérnie az API-kulcsot és a végpontot.

## <a name="notebook"></a>Jegyzetfüzet

Ha meg szeretné tudni, hogyan hívhatja meg a rendellenesség-érzékelő API-t, próbálja ki ezt az [Azure notebookot](https://aka.ms/adNotebook). Ez a webkiszolgáló Jupyter Notebook bemutatja, hogyan küldhet API-kéréseket, és hogyan jelenítheti meg az eredményt.

A jegyzetfüzet futtatásához hajtsa végre a következő lépéseket:

1. Szerezzen be egy érvényes anomália-detektor API-előfizetési kulcsot és egy API-végpontot. Az alábbi szakasz útmutatást nyújt a regisztráláshoz.
1. Jelentkezzen be, és kattintson a jobb felső sarokban található klónozás elemre.
1. A klónozási művelet befejezése előtt törölje a "nyilvános" lehetőséget a párbeszédpanelen, ellenkező esetben a notebook, beleértve az előfizetési kulcsokat is, nyilvános lesz.
1. Kattintson **a Futtatás ingyenes számításkor** lehetőségre
1. Válassza ki az egyik jegyzetfüzetet.
1. Adja hozzá az érvényes anomália-érzékelő API- `subscription_key` előfizetési kulcsát a változóhoz. 
1. Módosítsa a `endpoint` változót a végpontra. Például:`https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. A felső menüsorban kattintson a **cella**, majd az **összes futtatása**elemre.

## <a name="workflow"></a>Munkafolyamat

Az anomália-detektor API egy REST-alapú webszolgáltatás, amely megkönnyíti a HTTP-kérelmeket és a JSON-elemzést elvégező programozási nyelv meghívását.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Regisztráció után:

1. Készítsen idősoros adatait, és alakítsa át érvényes JSON formátumra. Az adatelőkészítés során [ajánlott eljárásokat](concepts/anomaly-detection-best-practices.md) használhat a legjobb eredmények eléréséhez.
1. Küldjön egy kérelmet a rendellenesség-Kiderítő API-nak az adataival.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="algorithms"></a>Algoritmusok

* Tekintse meg ezt a technikai blogot, amely bemutatja az [Azure rendellenesség-érzékelő API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) -ját a motorháztető alatt lévő algoritmusokkal kapcsolatban.
* A Microsoft által fejlesztett élvonalbeli SR-CNN-algoritmusokról a Microsoft (KDD 2019 által elfogadott) [adatsorozat-anomália-észlelési szolgáltatást](https://arxiv.org/abs/1906.03821) ismertető cikkben olvashat.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Csatlakozás az anomália detektor közösségéhez

* Csatlakozás az [anomália-detektor Advisors csoportjához a Microsoft Teams](https://aka.ms/AdAdvisorsJoin) szolgáltatásban
* A kiválasztott [felhasználó által létrehozott tartalom](user-generated-content.md) megjelenítése

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Az idősoros adataiban észlelt rendellenességek észlelése az anomália-detektor használatával REST API](quickstarts/detect-data-anomalies-csharp.md)
* Az anomália érzékelő API [online bemutatója](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Az anomália detektor [REST API referenciája](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
