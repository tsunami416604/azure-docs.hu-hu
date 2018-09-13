---
title: Eszköz információk metaadatait a távoli figyelési megoldásban |} A Microsoft Docs
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
ms.openlocfilehash: 4efea316c05f566add3e175bc5bb18842225ede3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758161"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>A távoli figyelési előre konfigurált megoldásban az eszköz információk metaadatait

Az Azure IoT Suite távoli figyelési előre konfigurált megoldás azt mutatja be egy olyan megközelítés eszköz metaadatainak kezelése. Ez a cikk ismerteti az ebben a megoldásban vesz igénybe, hogy jobban megismerhesse módszer:

* Milyen eszköz metaadatait a megoldás tárolja.
* A megoldás hogyan kezeli a az eszköz metaadatait.

## <a name="context"></a>Környezet

A távoli figyelési előre konfigurált megoldás [Azure IoT Hub] [ lnk-iot-hub] adatokat küldeni a felhőbe az eszközök engedélyezéséhez. A megoldás három különböző helyeken tárolja az eszközökkel kapcsolatos információk:

| Hely | Tárolt adatok | Megvalósítás |
| -------- | ------------------ | -------------- |
| Eszközidentitás-jegyzék | Eszköz azonosítója, a hitelesítési kulcsokat, engedélyezett állapota | Az IoT hubhoz a beépített |
| Ikereszközök | Metaadatok: jelentett tulajdonságokat, a kívánt tulajdonságok, a címkék | Az IoT hubhoz a beépített |
| Cosmos DB | Parancs és mód előzményei | Egyéni megoldás |

IoT Hub tartalmaz egy [eszközidentitás-jegyzék] [ lnk-identity-registry] egy IoT hubot, és használja való hozzáférés kezelése [ikereszközök] [ lnk-device-twin] eszköz metaadatainak kezelése. Emellett van egy távoli figyelési megoldás-specifikus *eszközjegyzék* parancs és metódus előzményeinek tárolja. A távoli figyelési megoldás használ egy [Cosmos DB] [ lnk-docdb] adatbázis egy egyéni üzlet parancs és metódus előzményeinek megvalósításához.

> [!NOTE]
> A távoli figyelési előre konfigurált megoldás tartja a Cosmos DB-adatbázis szinkronban tartani az adatokat az eszközidentitás-jegyzékében. Az ugyanazon eszközazonosítót mindkettő használja az IoT hubhoz csatlakoztatott egyes eszközök egyedi azonosításához.

## <a name="device-metadata"></a>Eszköz metaadatait

Az IoT Hub fenntart egy [ikereszköz] [ lnk-device-twin] minden szimulált és a fizikai eszköz csatlakozik a távoli figyelési megoldáshoz. A megoldás ikereszközökkel kezelheti az eszközök társított metaadatokat. Az ikereszközök JSON-dokumentumok az IoT Hub által karbantartott, és a megoldás az IoT Hub API az ikereszközök kommunikál.

Az ikereszközök tárolják a metaadatok három típusa:

- *Jelentett tulajdonságokként* egy IoT hubra egy eszköz által küldött. A távoli figyelési megoldásban a szimulált eszközök jelentett tulajdonságokkal küld, indításkor és a **Eszközállapot módosítása** parancsok és metódusok. Megtekintheti a jelentett tulajdonságok a **eszközlista** és **eszközadatok** a megoldás portálján. Jelentett tulajdonságokat csak olvashatók.
- *Kívánt tulajdonságok* lekért IoT hub-eszközök által. A feladata, hogy minden szükséges konfiguráció módosítását az eszköz az eszköz. Feladata is a jelentést a módosítás jelentett tulajdonságként a hub eszközt. Beállíthat egy kívánt tulajdonságot a megoldásportálon keresztül.
- *A címkék* csak az ikereszközön léteznek, és a egy eszköz nem lesznek szinkronizálva. A megoldásportálon beállított címke értékeket, és használja őket, ha az eszközök listájának szűrése. A megoldás is használ egy címkét egy eszköz a megoldásportálon megjelenő ikon azonosításához.

Példa a jelentett tulajdonságok a szimulált eszközökről olyan gyártó, modellszám, szélesség és hosszúság. A szimulált eszközök a támogatott módszerek listája is jelentett tulajdonságként adja vissza.

> [!NOTE]
> A szimulált eszközkód csak a **Desired.Config.TemperatureMeanValue** és a **Desired.Config.TelemetryInterval** kívánt tulajdonságot használja az IoT Hubra visszaküldött jelentett tulajdonságok frissítéséhez. Az összes többi kívánt tulajdonságmódosítási kérelmet a rendszer figyelmen kívül hagyja.

Eszköz információk metaadatok JSON-dokumentumok az eszközök beállításjegyzék Cosmos DB-adatbázisban tárolt struktúrája a következő:

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
> Eszköz adatai az eszköz által az IoT hubnak küldött telemetriai adatok leíró metaadatok is tartalmazhatnak. A távoli figyelési megoldás használ a telemetria metaadatok szabhatja testre, hogyan jeleníti meg az irányítópult [dinamikus telemetria][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Életciklus

Először hozzon létre egy eszközt a megoldás portálján, amikor a megoldás létrehoz egy bejegyzést a Cosmos DB-adatbázis parancs és metódus-előzmények tárolására. Ezen a ponton a megoldás is hoz létre az eszköz egy bejegyzés az eszközidentitás-jegyzék, amely az eszközt az IoT Hub-hitelesítést használ a kulcsokat hoz létre. Az ikereszközök is létrehoz.

Amikor egy eszköz először csatlakozik a megoldáshoz, elküldi jelentett tulajdonságok és a egy eszközinformációs üzenetet. A jelentett tulajdonságértékeket a rendszer automatikusan menti az ikereszközben. A jelentett tulajdonságok közé tartozik az eszköz gyártója, sorozatszám, és a egy támogatott módszerek listája. Az eszközinformációs üzenetet az eszköz támogatja, beleértve minden parancs paraméterei vonatkozó adatokat a parancsok listáját tartalmazza. Amikor a megoldás ezt az üzenetet kap, frissíti az eszközadatokat a Cosmos DB-adatbázisban.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>A megoldás portálja az eszköz adatainak megtekintése és szerkesztése

Alapértelmezés szerint a megoldásportálon az eszközök listája megjeleníti az oszlopként, hogy a következő eszköztulajdonságokat: **állapot**, **DeviceId**, **gyártó**, **modell Szám**, **sorozatszám**, **belső vezérlőprogram**, **Platform**, **processzor**, és  **Telepített RAM**. Az oszlopokat testre szabhatja kattintva **Oszlopszerkesztő**. Az eszköztulajdonságok **szélességi** és **hosszúsági** a hely a Bing térkép a meghajtó az irányítópulton.

![Oszlopszerkesztő az eszközlistában][img-device-list]

Az a **. eszköz részletei** panel a megoldás portálján, szerkesztheti a kívánt tulajdonságok és címkék (amelyek a tulajdonságok írásvédettek).

![Eszköz részletei panel][img-device-edit]

A megoldás portálja segítségével az eszköz eltávolítása a megoldás. Egy eszköz eltávolításakor a megoldás a eszközbejegyzés távolít el eszközidentitás-jegyzék, és végül törli az ikereszköz. A megoldás is eltávolítja az eszközt a Cosmos DB-adatbázishoz kapcsolódó adatokat. Eszköz eltávolítása előtt le kell tiltani.

![Eszköz eltávolítása][img-device-remove]

## <a name="device-information-message-processing"></a>Eszköz információk üzenetfeldolgozás

Egy eszköz által küldött eszközinformációs üzeneteket nem azonos a telemetriai üzeneteket. Eszközinformációs üzeneteket tartalmaznak, az eszköz válaszolhat parancsokat, és bármely eszközparancs-előzmények. Magát az IoT Hub az eszközinformációs üzenetet az tárolt metaadatok nem rendelkezik, és feldolgozza az üzenetet, bármely eszköz – felhő üzeneteket dolgoz megegyező módon. A távoli figyelési megoldásban egy [Azure Stream Analytics] [ lnk-stream-analytics] (ASA) feladat beolvassa az üzeneteket az IoT hubról. A **DeviceInfo** szűrők üzeneteket, amelyek tartalmazzák a stream analytics-feladat **"Objektumtípus": "DeviceInfo"** , és továbbítja őket a **EventProcessorHost** üzemeltető példány futó webjobs-feladat. A logikai a **EventProcessorHost** példányt használ az eszköz azonosítója a Cosmos DB-bejegyzés található az adott eszköz, és frissítse a rekordot.

> [!NOTE]
> Eszközinformációs üzenetet az egy normál eszközről a felhőbe – az üzenet. A megoldás közötti eszközinformációs üzeneteket és a telemetriai üzeneteket ASA-lekérdezések használatával különbözteti meg.

## <a name="next-steps"></a>További lépések

Végzett tanulási, hogyan szabhatja testre az előre konfigurált megoldások, most is a egyéb funkcióit és képességeit az IoT Suite előre konfigurált megoldások bemutatása:

* [Prediktív karbantartási előre konfigurált megoldás áttekintése][lnk-predictive-overview]
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
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
