---
title: Eseményrács összehasonlítása, útválasztás az IoT Hub számára | Microsoft dokumentumok
description: Az IoT Hub saját üzenet-útválasztási szolgáltatást kínál, de integrálható az Event Griddel az eseményközzétételhez. Hasonlítsa össze a két funkciót.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906786"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Üzenetútválasztás és Eseményrács összehasonlítása az IoT Hubban

Az Azure IoT Hub lehetővé teszi az adatok streamelését a csatlakoztatott eszközökről, és integrálhatja ezeket az adatokat az üzleti alkalmazásokba. Az IoT Hub két módszert kínál az IoT-események más Azure-szolgáltatásokba vagy üzleti alkalmazásokba való integrálására. Ez a cikk ismerteti a két funkció, amely ezt a funkciót, így kiválaszthatja, hogy melyik lehetőség a legjobb a forgatókönyv.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub-üzenet-útválasztás:](iot-hub-devguide-messages-d2c.md)** Ez az IoT Hub-szolgáltatás lehetővé teszi a felhasználók számára, hogy az eszköz-felhő üzeneteket olyan szolgáltatásvégpontok, például az Azure Storage-tárolók, az Event Hubs, a Service Bus-várólisták és a Service Bus-témakörök. Az Útválasztás lekérdezési lehetőséget is biztosít az adatok szűrésére, mielőtt a végpontokhoz irányítanák. Az eszköz telemetriai adatok mellett [nem telemetriai eseményeket](iot-hub-devguide-messages-d2c.md#non-telemetry-events) is küldhet, amelyek műveletek et aktiválhatnak. 

**IoT Hub-integráció az Event Griddel:** Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi-előfizetési modellt használ. Az IoT Hub és az Event Grid együttműködve [integrálja az IoT Hub-eseményeket az Azure-ba és a nem Azure-szolgáltatásokba](iot-hub-event-grid.md)közel valós időben. Az IoT Hub [eszközeseményeket](iot-hub-event-grid.md#event-types) és telemetriai eseményeket is közzétesz.

## <a name="differences"></a>Eltérések

Bár mind az üzenet-útválasztás, mind az Eseményrács engedélyezi a riasztási konfigurációt, a kettő között van néhány kulcsfontosságú különbség. A részleteket lásd az alábbi táblázatban:

| Szolgáltatás | IoT Hub-üzenettovábbítás | Az IoT Hub integrációja az Event Griddel |
| ------- | --------------- | ---------- |
| **Eszközüzenetek és események** | Igen, az üzenet-útválasztás telemetriai adatokhoz, az eszköz ikerváltozásainak jelentéséhez, az eszköz életciklus-eseményeihez és a digitális ikermódosítási eseményekhez használható (az [IoT Plug and Play nyilvános előzetes verzió](../iot-pnp/overview-iot-plug-and-play.md)része). | Igen, az Event Grid telemetriai adatokhoz használható, de jelentheti az eszközöket, amikor az iot hubról létrehoznak, törölnek, csatlakoztatnak és levannak választva |
| **Rendezés** | Igen, az események rendezése megmarad.  | Nem, az események sorrendje nem garantált. | 
| **Szűrés** | Az üzenetalkalmazás tulajdonságainak, az üzenetrendszer tulajdonságainak, az üzenettörzsnek, az ikereszköz-címkéknek és az ikereszköz-tulajdonságoknak a bővített szűrése. A szűrés nem vonatkozik a digitális ikermódosítási eseményekre. Példák: [Üzenetátirányítás lekérdezés szintaxisa](iot-hub-devguide-routing-query-syntax.md). | Szűrés az egyes események eseménytípusa, tématípusa és attribútumai alapján. Példák: [Az Események szűrésének ismertetése az Eseményrács-előfizetések alkalmazásban.](../event-grid/event-filtering.md) Telemetriai eseményekre való feliratkozáskor további szűrőket alkalmazhat az adatokra az üzenetek tulajdonságainak, az üzenettörzsnek és az ikereszköznek az IoT Hubban történő szűréséhez, mielőtt közzétenné az Event Gridben. Tekintse [meg, hogyan szűrheti az eseményeket.](../iot-hub/iot-hub-event-grid.md#filter-events) |
| **Végpontok** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus-üzenetsor</li> <li>A Service Bus témakörei</li></ul><br>Fizetős IoT Hub-skus (S1, S2 és S3) 10 egyéni végpontok. 100 útvonal hozható létre Az IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Egyéni témakörök</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Külső szolgáltatások webhookokon keresztül</li></ul><br>Az IoT Hubonként 500 végpont támogatott. A végpontok legfrissebb listáját az [Eseményrács eseménykezelői című témakörben tarol.](../event-grid/overview.md#event-handlers) |
| **Költségek** | Az üzenetek útválasztása nem számít fel külön díjat. Csak a telemetriai adatok be- és az IoT Hub ba kerül felszámolásra. Ha például egy üzenetet három különböző végpontra irányítanak, akkor csak egy üzenetért kell fizetnie. | Az IoT Hub nem számít fel díjat. Az Event Grid az első 100 000 műveletet kínálja havonta ingyen, majd 0,60 $ /millió műveletet. |

## <a name="similarities"></a>Hasonlóságok

Az IoT Hub-üzenet-útválasztás és az Event Grid is hasonlóságokkal rendelkezik, amelyek közül néhány az alábbi táblázatban található:

| Szolgáltatás | IoT Hub-üzenettovábbítás | Az IoT Hub integrációja az Event Griddel |
| ------- | --------------- | ---------- |
| **Az üzenetek maximális mérete** | 256 KB, eszközről felhőre | 256 KB, eszközről felhőre |
| **Megbízhatóság** | Magas: Minden üzenetet legalább egyszer továbbít a végpontnak minden útvonalhoz. Egy órán belül lejár minden olyan üzenet, amely et nem kézbesítenek. | Magas: Minden üzenetet a webhook legalább egyszer minden előfizetéshez kézbesíti. Lejáratminden olyan eseményt, amely 24 órán belül nem lesz kézbesítve. | 
| **Méretezhetőség** | Magas: Úgy optimalizálva, hogy támogassa az egyszerre csatlakoztatott eszközök millióit, amelyek több milliárd üzenetet küldenek. | Magas: Régiónként másodpercenként 10 000 000 esemény továbbítására képes. |
| **Késés** | Alacsony: Közel valós időben. | Alacsony: Közel valós időben. |
| **Küldés több végpontra** | Igen, egyetlen üzenetet küldhet több végpontnak. | Igen, egyetlen üzenetet küldhet több végpontnak.  
| **Biztonság** | Az Iot Hub eszközönkénti identitás- és visszavonható hozzáférés-vezérlést biztosít. További információt az [IoT Hub hozzáférés-vezérlése című témakörben talál.](iot-hub-devguide-security.md) | Event Grid ellenőrzést biztosít három ponton: esemény-előfizetések, esemény közzététele és webhook esemény kézbesítése. További információt az [Event Grid biztonsága és hitelesítése](../event-grid/security-authentication.md)című témakörben talál. |

## <a name="how-to-choose"></a>Mi alapján válasszon?

Az IoT Hub üzenet-útválasztása és az IoT Hub-integráció az Event Griddel különböző műveleteket hajt végre a hasonló eredmények elérése érdekében. Mindketten adatokat az IoT Hub-megoldásból, és továbbítja azt, hogy más szolgáltatások reagálhassanak. Szóval hogyan dönti el, melyiket használja? Gondold át a következő kérdéseket, amelyek segítenek a döntésed irányításában: 

* **Milyen típusú adatokat küld a végpontok?**

   Az IoT Hub-üzenet-útválasztás használata, ha telemetriai adatokat kell küldenie más szolgáltatásoknak. Az üzenet-útválasztás lehetővé teszi az üzenetalkalmazás és a rendszer tulajdonságainak, az üzenettörzsnek, az ikercímkéknek és az ikereszköz-tulajdonságoknak a lekérdezését is.

   Az IoT Hub-integráció az Event Griddel az IoT Hub szolgáltatásban előforduló eseményekkel működik. Ezek az IoT Hub-események közé tartozik a telemetriai adatok, létrehozott, törölt, csatlakoztatott és leválasztott eszköz. Telemetriai eseményekre való feliratkozáskor további szűrőket alkalmazhat az adatokra az üzenetek tulajdonságainak, az üzenettörzsnek és az ikereszköznek az IoT Hubban történő szűréséhez, mielőtt közzétenné az Event Gridben. Tekintse [meg, hogyan szűrheti az eseményeket.](../iot-hub/iot-hub-event-grid.md#filter-events)

* **Milyen végpontok kell kapniezt az információt?**

   Az IoT Hub üzenet-útválasztás a korlátozott számú egyedi végpontot és végponttípust támogatja, de összekötőket hozhat létre az adatok és események további végpontokra való átirányításához. A támogatott végpontok teljes listáját az előző szakasz táblázatában található. 

   Az IoT Hub-integráció az Event Griddel 500 végpontot támogat Az IoT Hub és a végponttípusok szélesebb választéka. Natív módon integrálható az Azure Functions, a Logic Apps, a Storage és a Service Bus várólistáival, és webhookokkal együttműködve kiterjeszti az adatok küldését az Azure szolgáltatási ökoszisztémán kívülre és harmadik fél üzleti alkalmazásaiba.

* **Számít, ha adatai sorrendben érkeznek?**

   Az IoT Hub-üzenet-útválasztás az üzenetek küldésének sorrendjét tartja fenn, így azok ugyanúgy érkeznek.

   Az Event Grid nem garantálja, hogy a végpontok ugyanabban a sorrendben kapják meg az eseményeket, mint amilyen sorrendben történtek. Azokban az esetekben, amikor az üzenetek abszolút sorrendje jelentős, és/vagy amikor a fogyasztónak megbízható egyedi azonosítóra van szüksége az üzenetekhez, javasoljuk az üzenet-útválasztás használatát. 

## <a name="next-steps"></a>További lépések

* További információ az [IoT Hub üzenet-útválasztásáról](iot-hub-devguide-messages-d2c.md) és az [IoT Hub végpontjairól.](iot-hub-devguide-endpoints.md)
* További tudnivalók az [Azure Event Grid](../event-grid/overview.md) szolgáltatásról.
* Az Üzenetútvonalak létrehozásáról az [IoT Hub-eszközről a felhőbe irányuló üzenetek feldolgozása az útvonalakkal](../iot-hub/tutorial-routing.md) oktatóanyag című témakörben olvashat.
* Próbálja ki az Event Grid-integrációt az [Azure IoT Hub-eseményekről szóló e-mail-értesítések logic apps használatával történő küldésével.](../event-grid/publish-iot-hub-events-to-logic-apps.md)
