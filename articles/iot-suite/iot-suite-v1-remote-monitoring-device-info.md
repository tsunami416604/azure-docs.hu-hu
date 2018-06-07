---
title: Eszköz információk metaadatait a távoli felügyeleti megoldás |} Microsoft Docs
description: Az Azure IoT távoli figyelési előre konfigurált megoldás és architektúrájának leírása.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 80f03a4cef1d79e819c59ca68a786776a5c4edb7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636096"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Eszköz információk metaadatait a távoli felügyeleti előkonfigurált megoldás

A távoli felügyeleti előkonfigurált megoldás Azure IoT Suite egy eszköz metaadatait kezelésére szolgáló módszert mutatja be. Ez a cikk ismerteti az ehhez a megoldáshoz szükséges ahhoz, hogy tisztában módszerrel:

* Milyen eszköz metaadatait tárolja, a megoldás.
* A megoldás hogyan kezeli a az eszköz metaadatait.

## <a name="context"></a>Környezet

A távoli megfigyelési előre konfigurált megoldás által használt [Azure IoT Hub] [ lnk-iot-hub] ahhoz, hogy az eszközök szeretnék adatokat küldeni a felhőben. A megoldás három különböző helyeken eszközökkel kapcsolatos információkat tárolja:

| Hely | Tárolt információ | Megvalósítás |
| -------- | ------------------ | -------------- |
| Identitásjegyzékhez | Eszközazonosító, a hitelesítési kulcsokat, engedélyezett állapota | A beépített IoT-központ |
| Eszköz twins | Metaadatok: jelentett tulajdonságai, a kívánt tulajdonságokat, a címkék | A beépített IoT-központ |
| Cosmos DB | A parancs és metódus előzményei | Egyéni megoldás |

Az IoT-központ tartalmazza a [eszközidentitási] [ lnk-identity-registry] az IoT-központ és a hozzáférés kezelésére [eszköz twins] [ lnk-device-twin] kezelése eszköz metaadatait. Is van a távoli felügyeleti megoldás-specifikus *eszközbeállításjegyzékben* , amely tárolja a parancs és metódus előzményeit. A távoli felügyeleti megoldás az olyan [Cosmos DB] [ lnk-docdb] adatbázis parancs és metódus előzményekhez szükséges egyéni tárolót végrehajtásához.

> [!NOTE]
> A távoli felügyeleti előkonfigurált megoldás tartja az eszközidentitási szinkronban vannak az adatokat a Cosmos DB adatbázisban. Mindkettő az ugyanazon eszközazonosítót használja a az IoT hub csatlakoztatott minden eszközhöz egyedi azonosításához.

## <a name="device-metadata"></a>Eszköz metaadatait

Az IoT-központ tart fenn a [eszköz két] [ lnk-device-twin] az egyes szimulált és a fizikai eszközök távoli felügyeleti megoldás csatlakozik. A megoldás eszköz twins eszközök társított metaadatokat kezelésére használ. Egy eszköz iker IoT-központ által kezelt JSON-dokumentum, és a megoldás az IoT Hub API-t használ eszköz twins kommunikál.

Egy eszköz iker metaadatok három típusú tárolja:

- *Tulajdonságok jelentett* az IoT-központ eszköz által küldött. A távoli felügyeleti megoldás, a szimulált eszköz jelentett tulajdonságok küldött, indításkor és a válasz **módosította az eszköz állapotát,** parancsok és módszerek. A jelentésben szereplő tulajdonságok megtekintéséhez a **eszközlista** és **. eszköz részletei** a megoldás portálon. Jelentett tulajdonságok csak olvashatók.
- *Szükségeskonfiguráció-tulajdonságok* olvassa be az IoT hub-eszközök által. A feladata az eszköz minden szükséges konfigurációs módosítása az eszközön. Az eszközt, hogy a módosítás visszajelzést szeretne az elosztóhoz jelentett tulajdonságként felelőssége egyben. Beállíthatja, hogy a megoldás portálon keresztül kívánt tulajdonság értéke.
- *Címkék* csak az eszköz iker szerepel, és egy eszköz nem lesznek szinkronizálva. A megoldás portálon beállított előfizetéscímkék értékeit, és az eszközök listájának szűrésekor használja őket. A megoldás is használ egy címkét egy eszköz számára a megoldás portálon megjelenő ikon azonosításához.

Példa jelentett, a szimulált eszközök tulajdonságai közé tartozik a gyártó, a modell számának, a szélességi és hosszúsági. Szimulált eszköz támogatott metódusok is jelentett tulajdonságaként adja vissza.

> [!NOTE]
> A szimulált eszközkód csak a **Desired.Config.TemperatureMeanValue** és a **Desired.Config.TelemetryInterval** kívánt tulajdonságot használja az IoT Hubra visszaküldött jelentett tulajdonságok frissítéséhez. Az összes többi kívánt tulajdonság változáskérések figyelmen kívül lesznek hagyva.

Egy eszköz információk metaadatok JSON-dokumentum az eszköz beállításjegyzék Cosmos DB adatbázisban tárolja az alábbi szerkezettel rendelkezik:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Eszköz adatai is tartalmazhatnak metaadatokra ahhoz, hogy az eszköz küld az IoT-központ a telemetriai adatok ismertetik. A távoli felügyeleti megoldás a telemetriai adatok metaadatok használja az irányítópult megjelenítésének testreszabása [dinamikus telemetriai][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Életciklus

Első létrehozásakor egy eszközt a megoldás portálon, a megoldás a Cosmos DB adatbázisban tárolják a parancs és metódus előzmények létrehoz egy tételt. Ezen a ponton a megoldás is létrehoz az eszköz az eszközidentitási, amely állít elő, a kulcsok az eszköz segítségével hitelesíti az IoT-központ a. Egy eszköz iker is létrehoz.

Amikor egy eszköz először csatlakozik a megoldás, és küldi el jelentett tulajdonságai egy eszköz tájékoztató üzenetet. A jelentett tulajdonságértékek automatikusan megtörténik az eszköz iker a. A jelentett tulajdonságai közé tartozik az eszköz gyártóját, a modell számának, a sorozatszám és a támogatott módszerek listáját. Az eszköz információs üzenet beleértve bármilyen parancs paraméterei vonatkozó adatokat az eszköz támogatja a parancsok listáját tartalmazza. Ha a megoldás ezt az üzenetet kap, frissíti az eszköz adatait, a Cosmos DB adatbázisban.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>A megoldás portálon eszköz adatainak megtekintése és szerkesztése

Alapértelmezés szerint az eszközök listája a megoldás portál megjeleníti az oszlopok a következő tulajdonságai: **állapot**, **DeviceId**, **gyártó**, **modellszámát**, **sorozatszám**, **belső vezérlőprogram**, **Platform**, **processzor**, és **telepített memória**. Testre szabhatja az oszlopok kattintva **oszlop szerkesztő**. Az eszköz Tulajdonságok **szélesség** és **hosszúság** a helyet, a Bing térképek meghajtó az irányítópulton.

![Oszlop-szerkesztőt az eszközök listája][img-device-list]

Az a **. eszköz részletei** ablaktábla kívánt tulajdonságokkal és címkék szerkesztheti a megoldás portálon (csak olvasható tulajdonságok jelentette).

![Eszköz részletező ablaktábláján][img-device-edit]

A megoldás portál segítségével az eszköz eltávolítása a megoldás. Egy eszköz eltávolításakor a megoldás eltávolítja a identitásjegyzékhez az eszköz bejegyzést, és majd törli az eszköz a két. A megoldás is eltávolítja az eszközt a Cosmos DB adatbázisból kapcsolatos adatokat. Eszköz eltávolítása előtt le kell tiltani.

![Eszköz eltávolítása][img-device-remove]

## <a name="device-information-message-processing"></a>Eszköz információk üzenet feldolgozása

Eszköz információs üzenetet küldött egy eszköz különbözőek a telemetria-üzeneteket. Eszköz információs üzenetet eszköz válaszolhassanak a parancsokat, és bármely parancselőzmények tartalmazza. Maga az IoT-központ nem ismeri a egy eszköz információs üzenet tárolt metaadatok, és feldolgozza az üzenetet ugyanúgy bármely eszközről a felhőbe üzenetet feldolgozza. A távoli felügyeleti megoldás egy [Azure Stream Analytics] [ lnk-stream-analytics] (ASA) feladat olvassa be az IoT-központ az üzeneteket. A **deviceinfo információja** stream analytics-feladat tartalmazó üzenetek szűrők **"Objektumtípus": "Deviceinfo információja"** , és továbbítja őket a **EventProcessorHost** fut, web feladat, amely. A logikai a **EventProcessorHost** példány található a Cosmos DB rekord az adott eszközt, és frissítse a bejegyzést az Eszközazonosítót használja.

> [!NOTE]
> Egy eszköz információs üzenet jelenik meg egy szabványos eszközről a felhőbe üzenet. A megoldás különböztet eszköz információs üzenetet és telemetriai üzenetek ASA lekérdezések használatával.

## <a name="next-steps"></a>További lépések

Miután befejezte a tanulási, hogyan szabhatja testre az előkonfigurált megoldásokat, ismerje meg egyes egyéb szolgáltatásokat és képességeket az IoT Suite előre konfigurált megoldások:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése][lnk-predictive-overview]
* [Gyakran ismételt kérdések az IoT Suite-ról][lnk-faq]
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-v1-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-v1-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-v1-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]:../iot-accelerators/securing-iot-ground-up.md
