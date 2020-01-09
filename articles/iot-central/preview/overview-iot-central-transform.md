---
title: Átalakítás áttekintése
description: További információ az átalakításról
author: viv-liu
ms.author: viviali
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3df839738aaf28de3b32eee6e30f15376a00cc0d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434884"
---
# <a name="transform-your-iot-data-preview-features"></a>Alakítsa át a IoT-adatait (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Az alkalmazási platformként a IoT Central számos fő aspektust biztosít a IoT-információk átalakításához a gyakorlatban hasznosítható eredményekkel rendelkező üzleti elemzésekben. A IoT Central lehetővé teszi, hogy a IoT-adatait külső rendszerekre terjessze ki az üzletági alkalmazásokkal és egyéni alkalmazásokkal való integráció létrehozásához. Az Eszközállapot és a műveletek figyeléséhez olyan szabályokat hozhat létre, amelyek a technikusokat a mobil szolgáltatáson keresztül értesítik. A nyers telemetria-adatszolgáltatások Azure-ba való exportálásával egyedi elemzési és gépi tanulási célú üzleti elemzéseket készíthet. Olyan szolgáltatásokat és eszközöket hozhat létre, amelyek figyelik és vezérelhetik az eszközöket, és felügyelik IoT Central alkalmazást a nyilvános API-k használatával. 

![Átalakítás IoT Central – áttekintés](media/overview-iot-central-transform/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>Eszközök állapotának és műveleteinek figyelése szabályok használatával
Ha a gépek csatlakoztatva vannak és adatokat küldenek, meghatározhatja, hogy mely gépek észlelnek problémákat, vagy hibaüzeneteket küldjön, hogy a szokásosnál minimális állásidővel lehessen futtatni őket. A IoT Central alkalmazásban szabályokat hozhat létre, amelyekkel figyelheti az eszközök által küldött telemetria, és riasztást kaphat, ha túllépte a küszöbértéket, vagy egy adott esemény üzenetet küldött. A szabályokhoz olyan műveleteket is konfigurálhat, mint például az e-mail-műveletek és a webhookok, amelyek értesítik a megfelelő személyeket és a megfelelő alsóbb rétegbeli rendszereket. További információ a szabályokról.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Egyéni elemzések és feldolgozás futtatása az exportált adatain
Ha egyéni elemzési folyamatokkal szeretné feldolgozni a nyers IoT-telemetria, és a végeredményt tárolja, létrehozhat olyan, nagyvállalati szintű üzleti elemzéseket, mint például a gépi hatékonysági trendek meghatározása vagy a jövőbeli energiafelhasználás előrejelzése a gyári szinten. Adatexportálást hozhat létre a IoT Central alkalmazásban a telemetria, az eszköz tulajdonságainak és az életciklusának exportálásához, és az eszköz sablonja más Azure-szolgáltatásokra is módosítja az adatokat, így elemezheti, tárolhatja és megjelenítheti a leginkább értékes eszközök adatait. További információ az adatexportálásról.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Egyéni IoT-megoldások és-integrációk létrehozása API-kkal
Olyan IoT-megoldásokat hozhat létre, mint például a mobil Companion-alkalmazások, amelyek távolról vezérelhetik az eszközöket, és új eszközöket állíthatnak be, illetve a meglévő üzletági alkalmazásokkal való egyéni integrációt a IoT-eszközökhöz és az Ön üzleti igényeihez igazított adataihoz. Az eszköz modellezésének, bevezetésének, felügyeletének és adatelérésének gerincét IoT Central használhatja. További információ a nyilvános API-ról ebben a tanulási modulban.
