---
title: Az Azure IoT Central architekturális fogalmak |} A Microsoft Docs
description: Ez a cikk bemutatja az Azure IoT Central architektúrájával kapcsolatos alapfogalmak
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4bc9a79576c3165585a4a2c897bd41bfb77c080c
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693134"
---
# <a name="azure-iot-central-architecture"></a>Az Azure IoT Central architektúrája

Ez a cikk a Microsoft Azure IoT Central-architektúra áttekintése.

![Legfelső szintű architektúra](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Eszközök

Eszközök exchange-adatok az Azure IoT Central alkalmazáshoz. Egy eszköz a következőket teheti:

- Például a telemetriai adatok mérések küldése.
- Beállítások szinkronizálása az alkalmazását.

Az Azure IoT Central egy eszközt az alkalmazással is exchange-adatok egy eszköz sablonban megadott. Eszköz sablonokkal kapcsolatos további információkért lásd: [metaadatok felügyeleti](#metadata-management).

Hogyan eszközök csatlakoztatása az Azure IoT Central alkalmazásnak kapcsolatos további információkért lásd: [eszközkapcsolatok](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Átjáró

Az Azure IoT Central egy felhőátjáróhoz, amely lehetővé teszi az eszköz csatlakoztatása Azure IoT Hub használja. Az IoT Hub a következőket:

- A felhőbeli nagy mennyiségű adatbetöltés.
- Eszközök kezelése.
- Biztonságos kapcsolat az eszközzel.

Az IoT Hub kapcsolatos további információkért lásd: [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Eszköz csatlakoztatása az Azure IoT Central kapcsolatos további információkért lásd: [eszközkapcsolatok](concepts-connectivity.md).

## <a name="data-stores"></a>Adattárolók

Az Azure IoT Central alkalmazásadat tárol a felhőben. Alkalmazásadatok tárolása tartalmazza:

- Eszköz sablonok.
- Eszközidentitások.
- Eszköz metaadatait.
- Felhasználó és szerepkör-adatokat.

Az Azure IoT Central egy alkalommal sorozat a mérési adatokat az eszközök által küldött használ. Idősorozat-adatok az analytics szolgáltatás által használt eszközökről.

## <a name="analytics"></a>Elemzés

Az elemzési szolgáltatás, amely megjeleníti az alkalmazás egyéni jelentésadatok generálása felelős. Az operátor is [testre szabhatja az analytics](howto-create-analytics.md) az alkalmazásban jeleníthető meg. Az elemzés szolgáltatás a beépített [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) és a mérési adatokat az eszközök által küldött dolgozza fel.

## <a name="rules-and-actions"></a>Szabályok és műveletek

[Szabályok és műveletek](howto-create-telemetry-rules.md) szorosan együtt, automatizálhatja a feladatokat az alkalmazásban lévő munkahelyi. A jelentéskészítő adhat meg szabályokat, például a hőmérséklet egy megadott küszöbértéket meghaladó eszköz telemetriai adatokon alapul. Az Azure IoT Central egy adatfolyam-feldolgozó alapján határozza meg, ha teljesülnek-e a szabály feltételeit. Amikor egy szabály a feltétel teljesül, elindítja a szerkesztő által meghatározott műveletet. Például egy művelet küldhet egy e-mailben értesíteni mérnökként, hogy egy eszköz hőmérséklete túl magas.

## <a name="metadata-management"></a>Metaadatok kezelése

Azure IoT Central alkalmazáshoz eszközsablonok viselkedését és különböző típusú eszköz képesség határoz meg. Például hűtő eszköz sablont adja meg a telemetriát küld egy hűtőszekrények alkalmazására.

![Sablon-architektúra](media/concepts-architecture/template_architecture.png)

Egy eszköz sablonban:

- **Mértékek** adja meg az eszköz telemetriai adatokat küld az alkalmazáshoz.
- **Beállítások** adja meg a konfigurációk, az operátornak állíthatja be.
- **Tulajdonságok** adja meg a metaadatokat, amelyek az operátornak állíthatja be.
- **Szabályok** automatizálhatja az eszközről küldött adatok alapján az alkalmazás viselkedését.
- **Az irányítópultok** olyan eszköz, az alkalmazás testre szabható nézetek.

Egy alkalmazás egy vagy több szimulált és valódi eszközön minden egyes eszköz sablon alapján is rendelkezik.

## <a name="data-export"></a>Adatok exportálása

Az Azure IoT Central alkalmazáshoz is [folyamatosan exportálhatja az adatokat](howto-export-data-event-hubs-service-bus.md) a saját Azure Event Hubs és Azure Service Bus-példányok. Az Azure Blob storage-fiókjában az adatok rendszeres időközönként exportálja. IoT-központ mérések, eszközök és eszközsablonok exportálhatja.

## <a name="batch-device-updates"></a>Batch-eszköz frissítése

Az Azure IoT Central alkalmazáshoz is [hozzon létre és futtathat feladatokat](howto-run-a-job.md) csatlakoztatott eszközök kezelésére. Ezek a feladatok lehetővé teszik a tömeges eszköz tulajdonságait és a beállításokat, vagy futtatnak parancsokat. Például létrehozhat egy feladatot a több hűtött eladóautomaták ventilátor sebességének növelése érdekében.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

Egy [rendszergazdai hozzáférési szabályokat definiálhat](howto-administer.md) az előre definiált szerepkörök használatával az Azure IoT Central alkalmazáshoz. A rendszergazda felhasználókat rendelhet szerepköröket, amelyek meghatározzák, milyen területeken, az alkalmazás a felhasználónak hozzáférése van.

## <a name="security"></a>Biztonság

Azure IoT Central belül biztonsági szolgáltatások a következők:

- Az átvitel során, míg az inaktív adatok titkosítása.
- Hitelesítés Azure Active Directory vagy a Microsoft Account biztosítunk. A kétfaktoros hitelesítést is támogatja.
- Teljes bérlők elkülönítését.
- Eszköz biztonság.

## <a name="ui-shell"></a>Felhasználói felület rendszerhéj

A felhasználói felület shell egy modern, rugalmas, HTML5-alapú böngészőalapú alkalmazás.
A rendszergazda testre szabható az alkalmazás a felhasználói felület alkalmazása egyéni témákat, és a saját egyéni erőforrások átirányítása súgóhivatkozások módosítása. Felhasználói felület testreszabásával kapcsolatos további információkért lásd: [testreszabása az Azure IoT Central felhasználói felület](howto-customize-ui.md) cikk.

Az üzemeltető alkalmazás személyre szabott irányítópultokat hozhat létre. Számos olyan irányítópultokat, amelyek más adatok megjelenítése és a kettő közötti váltás rendelkezhet.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az Azure IoT Central architektúrája,-e a javasolt következő lépésre kapcsolatos [eszközkapcsolatok](concepts-connectivity.md) az Azure IoT Central.