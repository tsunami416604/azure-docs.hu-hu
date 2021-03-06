---
title: Mi az az Anomaly Detector API?
titleSuffix: Azure Cognitive Services
description: Az anomáliák Kiderítő API algoritmusai segítségével alkalmazhatja a anomáliák észlelését az idősorozat-adatain.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 11/23/2020
ms.author: mbullwin
keywords: anomáliák észlelése, gépi tanulás, algoritmusok
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 1a13b962a000e6e3b2143dc23f94b63c122bd963
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999100"
---
# <a name="what-is-the-anomaly-detector-api"></a>Mi az az Anomaly Detector API?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A rendellenesség-Kiderítő API lehetővé teszi, hogy a gépi tanulás ismerete nélkül figyelje és azonosítsa az idősoros adataiban felmerülő rendellenességeket. A rendellenesség-érzékelő API algoritmusai úgy vannak kialakítva, hogy automatikusan azonosítják és alkalmazzák a legjobban illeszkedő modelleket az adataihoz, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől. Az idősorozat-adatok használatával az API meghatározza a rendellenességek észlelésére, a várt értékekre vonatkozó határokat, és hogy mely adatpontok rendellenesek.

![A szolgáltatási kérelmekben szereplő minták változásainak észlelése](./media/anomaly_detection2.png)

Az anomália detektor használata nem igényel korábbi felhasználói élményt a gépi tanulásban, és a REST API lehetővé teszi a szolgáltatás egyszerű integrálását az alkalmazásokba és folyamataiba.

## <a name="features"></a>Szolgáltatások

Az anomáliák detektorral automatikusan észlelheti a rendellenességeket az idősorozat-adataiban, vagy valós időben végezheti el azokat.

|Funkció  |Leírás  |
|---------|---------|
|Anomáliák észlelése valós időben. | A korábban látott adatpontok használatával észlelheti a folyamatos adatátviteli hibák észlelését, és meghatározhatja, hogy a legújabb-e anomália. A művelet létrehoz egy modellt a küldött adatpontok használatával, és meghatározza, hogy a célként megadott pont anomáli-e. Ha az API-t az Ön által létrehozott összes új adatpontra hívja fel, az adatait a létrehozásuk során figyelheti. |
|Az adatkészletekben lévő rendellenességek észlelése kötegként. | Az idősorozat használatával észlelheti az összes olyan rendellenességet, amely létezhet az összes adategységben. A művelet létrehoz egy modellt a teljes idősorozat-adataival, és minden olyan pontot, amely ugyanazzal a modellel lett elemezve.         |
|A módosítási pontok észlelése az adatkészletben kötegként. | Az idősorozat használatával észlelheti az adataiban található trend-változási pontokat. A művelet létrehoz egy modellt a teljes idősorozat-adataival, és minden olyan pontot, amely ugyanazzal a modellel lett elemezve.    |
| További információkat kaphat az adatairól. | Hasznos információkhoz juthat az adatokról és az észlelt rendellenességekről, beleértve a várt értékeket, a rendellenességek határait és a pozíciókat. |
| Állítsa be a anomália észlelési határait. | Az anomália detektor API automatikusan létrehoz határokat a anomáliák észleléséhez. Állítsa be ezeket a határokat az API-k adatrendellenességekre való érzékenységének növeléséhez vagy csökkentéséhez, és jobban illeszkedjen az adataihoz. |

## <a name="demo"></a>Bemutató

Tekintse meg ezt az [interaktív bemutatót](https://aka.ms/adDemo) , hogy megtudja, hogyan működik a rendellenesség-érzékelő.
A bemutató futtatásához létre kell hoznia egy anomália-detektor erőforrást, és le kell kérnie az API-kulcsot és a végpontot.

## <a name="notebook"></a>Jegyzetfüzet

Ha meg szeretné tudni, hogyan hívhatja meg a rendellenesség-Kiderítő API-t, próbálkozzon a [jegyzetfüzettel](https://aka.ms/adNotebook). Ez a Jupyter Notebook bemutatja, hogyan küldhet API-kéréseket, és hogyan jelenítheti meg az eredményt.

A jegyzetfüzet futtatásához hajtsa végre a következő lépéseket:

1. Szerezzen be egy érvényes anomália-detektor API-előfizetési kulcsot és egy API-végpontot. Az alábbi szakasz útmutatást nyújt a regisztráláshoz.
1. Jelentkezzen be, és kattintson a jobb felső sarokban található klónozás elemre.
1. A klónozási művelet befejezése előtt törölje a "nyilvános" lehetőséget a párbeszédpanelen, ellenkező esetben a notebook, beleértve az előfizetési kulcsokat is, nyilvános lesz.
1. Kattintson **a Futtatás ingyenes számításkor** lehetőségre
1. Válassza ki az egyik jegyzetfüzetet.
1. Adja hozzá az érvényes anomália-érzékelő API-előfizetési kulcsát a `subscription_key` változóhoz.
1. Módosítsa a `endpoint` változót a végpontra. Például: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. A felső menüsorban kattintson a **cella**, majd az **összes futtatása** elemre.

## <a name="workflow"></a>Munkafolyamat

Az anomália-detektor API egy REST-alapú webszolgáltatás, amely megkönnyíti a HTTP-kérelmeket és a JSON-elemzést elvégező programozási nyelv meghívását.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Regisztráció után:

1. Készítsen idősoros adatait, és alakítsa át érvényes JSON formátumra. Az adatelőkészítés során [ajánlott eljárásokat](concepts/anomaly-detection-best-practices.md) használhat a legjobb eredmények eléréséhez.
1. Küldjön egy kérelmet a rendellenesség-Kiderítő API-nak az adataival.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="algorithms"></a>Algoritmusok

* A használt algoritmusokról a következő technikai blogokban tájékozódhat:
    * [Az Azure Anomaly Detector API bemutatása](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Az SR-CNN algoritmus áttekintése az Azure-beli anomáliák Detektorában](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Ha többet szeretne megtudni a Microsoft által fejlesztett SR-CNN-algoritmusokról, olvassa el a Microsoft (KDD 2019 által elfogadott) [Time-sorozati anomália-észlelési szolgáltatást](https://arxiv.org/abs/1906.03821) .


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

Az API-funkciók helyszíni üzembe helyezéséhez [használjon anomáliát érzékelő tárolók használatával](anomaly-detector-container-howto.md) . A Docker-tárolók lehetővé teszik, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz.

## <a name="join-the-anomaly-detector-community"></a>Csatlakozás az Anomáliadetektor-közösséghez

* Csatlakozás az [anomália-detektor Advisors csoportjához a Microsoft Teams](https://aka.ms/AdAdvisorsJoin) szolgáltatásban
* A kiválasztott [felhasználó által létrehozott tartalom](user-generated-content.md) megjelenítése

## <a name="next-steps"></a>További lépések

* [Gyors útmutató: az idősoros adataiban észlelt rendellenességek észlelése az anomália-detektor használatával REST API](quickstarts/detect-data-anomalies-csharp.md)
* Az anomália érzékelő API [online bemutatója](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Az anomália detektor [REST API referenciája](https://aka.ms/anomaly-detector-rest-api-ref)
