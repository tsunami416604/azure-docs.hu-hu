---
title: Architektúra IoT Central Digital Distribution Center | Microsoft Docs
description: A IoT Centralhez készült Digital Distribution Center alkalmazás-sablon architektúrája
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 75d6591b13e21ce2914c3e407bad970522856c90
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957999"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>A IoT Central Digital Distribution Center alkalmazás-sablon architektúrája

A partnerek & ügyfél használhatja az alkalmazás sablonját & az alábbi útmutatást követve fejlesztheti a teljes körű **digitális terjesztési központ** megoldásait.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
2. Telemetria küldő és összesített bepillantást IoT Central
3. Az adattovábbítás a kívánt Azure-szolgáltatásba történik a manipulációhoz
4. Az olyan Azure-szolgáltatások, mint például az ASA vagy a Azure Functions kihasználhatják az adatfolyamok újraformázását és a kívánt Storage-fiókokba való elküldését 
5. A feldolgozott adatok tárolása a közel valós idejű műveletekhez vagy a hideg tároláshoz szükséges, és a további elemzési fejlesztések a ML-vagy a Batch-elemzés alapján történik. 
6. A Logic Apps a végfelhasználói üzleti alkalmazások különböző üzleti munkafolyamatainak kikapcsolására is használható

## <a name="details"></a>Részletek
A következő szakasz a fogalmi architektúra egyes részeit ismerteti

## <a name="video-cameras"></a>Videokamerák 
A videokamerák a digitális kapcsolatban álló, nagyvállalati szintű ökoszisztémában található elsődleges érzékelők. A gépi tanulás és a mesterséges intelligencia fejlődése, amely lehetővé teszi, hogy a videó strukturált adatokba kerüljön, és a felhőbe való küldés előtt feldolgozza azt a peremen. Az IP-kamerák használatával képeket rögzíthet, tömörítheti a kamerát, majd elküldheti a tömörített adatokhoz képest a video Analytics-folyamathoz tartozó GigE, vagy használhatja a képeket az érzékelőn, majd közvetlenül a Azure IoT Edge küldheti el ezeket a képeket , amely ezután tömöríti a video Analytics-folyamatokban való feldolgozás előtt. 

## <a name="azure-iot-edge-gateway"></a>Átjáró Azure IoT Edge
A "kamerák – érzékelők" és a peremhálózati munkaterhelések kezelése a Azure IoT Edge helyileg történik, és a kamera streamjét az elemzési folyamat dolgozza fel. A Azure IoT Edge video Analytics feldolgozási folyamata számos előnnyel jár, többek között a csökkent válaszidő, az alacsony sávszélességű felhasználás, ami alacsony késést okoz a gyors adatfeldolgozáshoz. A felhőbe csak a legfontosabb metaadatokat, elemzéseket vagy műveleteket kell elküldeni, ha további műveletre vagy vizsgálatra van szükség. 

## <a name="device-management-with-iot-central"></a>Eszközkezelés IoT Central 
Az Azure IoT Central egy megoldás-fejlesztői platform, amely leegyszerűsíti a IoT-eszköz & Azure IoT Edge az átjárók kapcsolódását, konfigurálását és felügyeletét. A platform jelentősen csökkenti az IoT, a műveletek és a kapcsolódó fejlesztések terheit és költségeit. Az ügyfelek & partnereink teljes körű vállalati megoldásokat hozhatnak létre a terjesztési központokban lévő digitális visszajelzési hurok eléréséhez.

## <a name="business-insights--actions-via-data-egress"></a>Üzleti elemzések & műveletek a kimenő adatforgalomon keresztül 
A IoT Central platform a folyamatos adatexportálás (CDE) és az API-k révén gazdag bővíthetőségi lehetőségeket biztosít. A telemetria adatfeldolgozási vagy nyers telemetria alapuló üzleti elemzések általában egy előnyben részesített üzletági alkalmazásba exportálhatók. Ez a webhook, a Service Bus, az Event hub vagy a blob Storage szolgáltatáson keresztül érhető el, amely a gépi tanulási modellek fejlesztéséhez, betanításához és üzembe helyezéséhez & további ismereteket biztosít.

## <a name="next-steps"></a>Következő lépések
* Útmutató a [digitális terjesztési központ sablonjának](./tutorial-iot-central-digital-distribution-center-pnp.md) üzembe helyezéséhez
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail-pnp.md)
* További információ a IoT Centralról [IoT Central áttekintés](../core/overview-iot-central-pnp.md)
