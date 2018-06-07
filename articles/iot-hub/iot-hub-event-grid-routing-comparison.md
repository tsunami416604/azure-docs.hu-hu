---
title: Esemény rács, IoT hub útválasztási összehasonlítása |} Microsoft Docs
description: Az IoT-központ lehetővé teszi a saját üzenet az útválasztási szolgáltatás, de együttműködik a esemény rács esemény közzététele. Hasonlítsa össze a két funkció.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 320320687e441a1296065eb9d0b7b12771036459
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636171"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Hasonlítsa össze az üzenet útválasztás és az IoT-központ esemény rács

Az Azure IoT-központ lehetővé teszi a csatlakoztatott eszközökből adatok folyamatos átviteléhez, és adatok integrálja az üzleti alkalmazások. Az IoT-központ az IoT-események integrálása más Azure-szolgáltatások vagy az üzleti alkalmazások két módszert kínál. A cikk ismerteti a két funkció, amely ezt a képességet biztosít, így kiválaszthatja, melyik lehetőség ideális a forgatókönyvéhez.

* **Az IoT-központ üzenet útválasztási**: Ez az IoT-központ szolgáltatás lehetővé teszi, hogy a felhasználók számára [eszközről a felhőbe üzenetek](iot-hub-devguide-messages-read-custom.md) az Azure Storage tárolók, az Event Hubs, a Service Bus-üzenetsorok és a Service Bus-üzenettémakörök például Szolgáltatásvégpontok. Útválasztási szabályokat rugalmasság biztosítása a lekérdezésalapú útvonalak végrehajtásához. Lehetővé teszik [kritikus riasztások](iot-hub-devguide-messages-d2c.md) , amely-lekérdezések műveleteket indít, és a fejlécek és a szervezet alapulhat. 
* **Az IoT-központ integrációja esemény rács**: Azure esemény rács a közzététel használó teljes körűen felügyelt esemény útválasztási szolgáltatásokat-modell előfizetés. Az IoT-központ és az esemény rács [IoT-központ események integrálása az Azure és az Azure-szolgáltatások](iot-hub-event-grid.md), közel valós időben. 

## <a name="similarities-and-differences"></a>Az Hasonlóságok és különbségek

Üzenet-útválasztás és esemény rács engedélyezi a riasztás konfigurálásában, amíg nincsenek a kettő közötti fontosabb különbségeket. Tekintse meg a következő táblázat további részletek:

| Szolgáltatás | Az IoT-központ üzenet-útválasztás | Az IoT-központ integrációja esemény rács |
| ------- | --------------- | ---------- |
| **Eszközre küldött üzenetek** | Igen, üzenet-útválasztás használható telemetriai adatokat. | Nem, esemény rács csak használható nem telemetriai adatokat az IoT-központ események. |
| **Esemény típusa** | Igen, üzenet-útválasztás jelenthetik-e kettős módosításokat, és az eszköz életciklusa események. | Igen, esemény rács is tud jelentéseket, amikor az eszköz regisztrálva van az IoT-központ, és eszközök törlésekor. |
| **Rendezés** | Igen, az események megmarad.  | Események sorrendje nem, nem garantált. | 
| **Maximális méret** | 256 KB, eszköz-felhő | 64 KB |
| **Szűrés** | SQL-szerű nyelv szűrési gazdag támogatja a szűrést, a fejlécek és a szervezetek. Tekintse meg a [IoT-központ lekérdezési nyelv](iot-hub-devguide-query-language.md). | Szűrés alapuló utótag/eszköz azonosítóját, amely hierarchikus szolgáltatásokat, mint a tárolás esetén működik. |
| **Végpontok** | <ul><li>Eseményközpont</li> <li>Storage-blobba</li> <li>Service Bus-üzenetsor</li> <li>Service Bus-témák</li></ul><br>10 egyéni végpontokat fizetős IoT Hub termékváltozatok (S1, S2 és S3) korlátozódnak. az IoT-központ / 100 útvonalak hozhatók létre. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Eseményközpont</li> <li>Logic Apps</li> <li>Microsoft Flow</li> <li>Külső szolgáltatások Webhookok keresztül</li></ul><br>Végpontok legfrissebb listáját lásd: [esemény rács eseménykezelők](../event-grid/overview.md#event-handlers). |
| **Költségek** | Nincs külön díjmentes üzenet útválasztást. Csak az IoT hubhoz telemetriai érkező fel van töltve. Például ha egy üzenetet, három különböző végpontokra irányítása, díját is felszámítjuk csak egy üzenet. | Nincs az IoT-központ nem kell fizetni. Esemény rács kínál havonta az első 100 000 műveletek szabad, és majd $0,60 ezt követően millió műveleteket. |

Az IoT-központ üzenet útválasztási és esemény rács kell Hasonlóságok túl, a következő táblázat részletezi, amelyek:

| Szolgáltatás | Az IoT-központ üzenet-útválasztás | Az IoT-központ integrációja esemény rács |
| ------- | --------------- | ---------- |
| **Megbízhatóság** | Magas: Kézbesíti a minden üzenet legalább egyszer minden útvonal végpontja. Egy órán belül nem küldött összes üzenet lejár. | Magas: Kézbesíti a minden üzenet legalább egyszer az egyes előfizetésekhez webhook. Összes esemény, amely nem érkeznek meg 24 órán belül lejár. | 
| **Méretezhetőség** | Magas: Optimalizált milliárd üzenetek küldésekor egyidejűleg csatlakoztatott eszközök millióira támogatásához. | Magas: Útválasztási 10,000,000 az események másodpercenkénti régiónként képes. |
| **Késés** | Alacsony: Csaknem valós időben. | Alacsony: Csaknem valós időben. |
| **Több végpont küldése** | Igen, egy üzenet küldése több végpontot. | Igen, egy üzenet küldése több végpontot.  | 
| **Biztonság** | Az IOT-központ biztosít az eszközönkénti identitás- és visszavonható hozzáférés-vezérlés. További információkért lásd: a [IoT-központ hozzáférés-vezérlés](iot-hub-devguide-security.md). | Esemény rács nyújt három pontokon érvényesítése: esemény-előfizetések, esemény-közzétételi és webhook esemény kézbesítését. További információkért lásd: [esemény rács biztonsági és hitelesítési](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Segítség a választáshoz

Az IoT-központ üzenet útválasztás és az IoT-központ integráció esemény rácshoz hasonló eredmény elérése érdekében különböző műveleteket. Akkor is igénybe vehet az adatokat az IoT Hub-megoldásból, és adja át, hogy más szolgáltatások reagálhasson. Igen, hogyan Ön dönt, hogy melyiket használja? A fenti szakaszban leírt adatokat használja az alábbi kérdésekre a következő részekben talál a döntési: 

* **Milyen típusú adatokat akkor küldi a végpontok?**

   Használja az IoT-központ üzenet útválasztási Ha telemetriai adatokat küldeni a más szolgáltatásokkal rendelkezik. Üzenettovábbítás is lehetővé teszi, hogy üzenetfejlécek és üzenettörzs lekérdezése. 

   Az IoT-központ integráció esemény rácshoz eseményekre az IoT-központ szolgáltatás működik. Az IoT-központ esemény közé tartozik az eszköz létrehozását és törlését. 

* **Mely végpontokat kell ezeket az információkat kapni?**

   Az IoT-központ üzenet útválasztási támogatja a korlátozott végpontok, de átirányíthatja az adatokat és eseményeket, további végpontok-összekötőt hozhat létre. Támogatott végpontok teljes listáját lásd: a tábla az előző szakaszban. 

   Az IoT-központ integráció esemény rácshoz további végpontok támogatja. Az Azure-szolgáltatások ökoszisztémáján kívülre, valamint a külső üzleti alkalmazások útválasztási kiterjeszteni webhookok is együttműködik. 

* **Nem számít, hogy az adatok sorrendben érkeznek?**

   A sorrendet, amelyben üzenetek küldése történik, az IoT-központ üzenet útválasztási tart fenn, úgy, hogy ugyanúgy érkezésükkor.

   Esemény rács nem garantálja, hogy a végpontok megkapja-e a események ugyanabban a sorrendben, hogy azok történt. Azonban az esemény-sémát, amely után az esemény érkezik a végpont sorrendje azonosításához használható időbélyeg tartalmazza. 

## <a name="next-steps"></a>További lépések

* További információ [IoT-központ üzenet útválasztási](iot-hub-devguide-messages-d2c.md) és a [IoT-központok végpontjai](iot-hub-devguide-endpoints.md).

* További információ [Azure esemény rács](../event-grid/overview.md)

* Próbálja ki az esemény rács integráció által [küldő értesítő e-mailek Azure IoT Hub eseményekről Logic Apps segítségével](../event-grid/publish-iot-hub-events-to-logic-apps.md)